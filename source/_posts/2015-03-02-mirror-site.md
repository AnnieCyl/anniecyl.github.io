title: "mirror-site"
date: 2015-03-02 10:30:24
tags:
- powershell
---

一直有打算好好学学 powershell，可是总是到要写的时候临时抱佛脚。前段时间也是因为要写自动生成镜像的脚本，又临时抱佛脚了一下。虽然可能写得不是很优雅完美，但是这些代码还涉及到挺多方面的。所以干脆贴上来，供以后参考。当然，以后还是要系统的学一下 powershell。
```
#region Variable 定义各种方法
function Clone($uri) {
    git.exe clone $uri
    return $LASTEXITCODE -eq 0
}

function CheckOut($commitHash){
    git.exe checkout $commitHash
    return $LASTEXITCODE -eq 0
}

function GetDirectoryName($uri) {
    if ($uri -cmatch '[^:/](?<USER_NAME>[\w-]+)/(?<REPO_NAME>[\w-]+)(?:\.git)?/?$') {
        return $Matches.REPO_NAME
    } else {
        return $null
    }
}

function ExtractZip($zipfilename, $destination){
    if(!(Test-Path $destination)){
        mkdir $destination | Out-Null
    }

    if(Test-Path $zipfilename)
    {   
        $shellApplication = New-Object -ComObject Shell.Application
        $zipPackage = $shellApplication.NameSpace($zipfilename)
        $destinationFolder = $shellApplication.NameSpace($destination)
        $destinationFolder.CopyHere($zipPackage.Items()) | Out-Null
    }
}

<#
# windows 服务相关的方法：停止服务，依赖于 nssm.exe
#>
function StopWindowsService($serviceName){
    $nssm = 'C:\ServiceTool\nssm.exe'
    $stopServiceArgs = @('stop', $serviceName, 'confirm')
    & $nssm $stopServiceArgs
}

<#
# windows 服务相关的方法：启动服务，依赖于 nssm.exe
#>
function StartWindowsService($serviceName){
    $nssm = 'C:\ServiceTool\nssm.exe'
    $startServiceArgs = @('start', $serviceName, 'confirm')
    & $nssm $startServiceArgs
}

<#
# windows 服务相关的方法：删除服务，依赖于 nssm.exe
#>
function RemoveWindowsService($serviceName){
    $nssm = 'C:\ServiceTool\nssm.exe'
    $removeServiceArgs = @('remove', $serviceName, 'confirm')
    & $nssm $removeServiceArgs
}

<#
# windows 服务相关的方法：安装服务，依赖于 nssm.exe
#>
function InstallWindowsService($serviceName, $application, $args){
    $nssm = 'C:\ServiceTool\nssm.exe'
    # 不用以下这种方式是因为会弹出创建服务的对话框
    #$installServiceArgs = @('install', $serviceName, $application, $args)
    #& $nssm $installServiceArgs
    & $nssm 'install' $serviceName $application $args
}

<#
# 将 git 项目 clone 到指定路径下的指定文件夹内
# $gitUrl：要 clone 的项目的 git 地址
# $targetPath：将项目 clone 到此路径下
#>
function CloneProject($gitUrl, $targetPath){
    Push-Location
    $projectName = GetDirectoryName $gitUrl
    if(Test-Path $targetPath\$projectName){
        Remove-Item $targetPath\$projectName -Force -Recurse
        'old cloned project deleted.'
    }
    Set-Location $targetPath
    Clone $gitUrl
    'new project cloned.'
    Pop-Location
}

<#
# 将指定项目复制到指定路径下
# $srcPath：要复制的项目的源路径
# $destPath：要复制的项目的目标路径
# $projectName：要复制的项目名称
#>
function CopyProject($srcPath, $destPath){    
    if(Test-Path $destPath){
        Remove-Item $destPath -Force -Recurse
        'old project deleted.'
    }
    Copy-Item $srcPath -Recurse $destPath
    'new project copied.'
}

<#
# 获取指定路径下的指定类型文件列表
# $path：要获取文件所在的路径
# $fileType：要获取的文件的类型
#>
function GetFileList($path, $fileType) {
    return Get-ChildItem -LiteralPath $path -Recurse -Include  $fileType | %{$_.FullName}
}

<#
# 将指定内容里的耗时的 js 引用替换掉
# $content：要进行替换的文件内容
#>
function ReplaceJs($content){
    $newContent = $content -creplace 'ga\.src(\s)*=(\s)*(.*)(google-analytics\.com)(.*)/ga\.js'';','ga.src = ''http://localhost/not-exist/ga.js'''
    $newContent = $newContent -creplace 'ajax\.googleapis\.com/ajax/libs/angularjs', 'www.cdnjs.net/ajax/libs/angular.js'
    $newContent = $newContent -creplace 'ajax\.googleapis\.com/ajax/libs/jquery', 'www.cdnjs.net/ajax/libs/jquery'
    $newContent = $newContent -creplace 'netdna\.bootstrapcdn\.com/bootstrap', 'www.cdnjs.net/ajax/libs/twitter-bootstrap'
    $newContent = $newContent -creplace 'cdnjs\.cloudflare\.com/ajax/libs/Cookies\.js', 'cdn.staticfile.org/Cookies.js'
    $newContent = $newContent -creplace '\w*\.google-analytics\.com', 'localhost/not-exist'
    $newContent = $newContent -creplace 'platform\.twitter\.com', 'localhost/not-exist'
    $newContent = $newContent -creplace 'connect\.facebook\.net', 'localhost/not-exist'
    $newContent = $newContent -creplace 'apis\.google\.com/js/platform.js', 'localhost/not-exist/platform.js'
    $newContent = $newContent -creplace 'cdnjs\.cloudflare\.com/ajax/libs/codemirror', 'www.cdnjs.net/ajax/libs/codemirror'
    $newContent = $newContent -creplace 'fonts\.googleapis\.com', 'fonts.useso.com'
    return $newContent
}

<#
# 遍历文件列表，将每个文件里面的耗时 js 引用替换掉
# $fileList：要进行替换的文件列表
# $encoding：指定文件进行读写使用的编码
#>
function Replace($fileList, $encoding){
    Foreach($file in $fileList)
    { 
        # 不用 Get-Content/Set-Content，是因为它们只支持指定的几种通用的编码。
        $fileContent = [System.IO.File]::ReadAllText($file, $encoding)
        $newContent = ReplaceJs $fileContent
        [System.IO.File]::WriteAllText($file, $newContent, $encoding)
    }
}

<#
# 生成 clone 后只需复制到服务器上即可的网站
# $gitUrl：API 文档的源码地址
# $cloneFolder：将 API 文档的源码 clone 到此文件夹下
# $serverFolder：API 文档在服务器上的文件夹
# $encoding：读写 API 文档源码的编码
#>
function GenerateWebsite($gitUrl, $cloneFolder, $serverFolder, $encoding){
    if(!(Test-Path $cloneFolder)){
        mkdir $cloneFolder | Out-Null
    }

    if(!(Test-Path $serverFolder)){
        mkdir $serverFolder | Out-Null
    }

    # 从 git 地址中获取项目名称
    $projectName = GetDirectoryName $gitUrl

    # 克隆项目
    CloneProject $gitUrl $cloneFolder

    # 更新服务器上的项目
    CopyProject $cloneFolder\$projectName $serverFolder\$projectName 

    # 获取该项目指定类型的文件列表
    $fileList = GetFileList $serverFolder\$projectName '*.html' 

    # 遍历文件列表，替换耗时 js
    Replace $fileList $encoding
}

<#
# 生成 clone 后用 Jekyll 编译后再复制到服务器上的网站
# $gitUrl：API 文档的源码地址
# $cloneFolder：将 API 文档的源码 clone 到此文件夹下
# $serverFolder：API 文档在服务器上的文件夹
# $encoding：读写 API 文档源码的编码
#>
function GenerateIonicWebsite($gitUrl, $cloneFolder, $serverFolder, $encoding){
    if(!(Test-Path $cloneFolder)){
        mkdir $cloneFolder | Out-Null
    }

    if(!(Test-Path $serverFolder)){
        mkdir $serverFolder | Out-Null
    }

    # 从 git 地址中获取项目名称
    $projectName = GetDirectoryName $gitUrl

    # 克隆项目
    CloneProject $gitUrl $cloneFolder

    # 生成静态网站
    Push-Location
    Set-Location $cloneFolder\$projectName
    jekyll build
    Pop-Location

    # 更新服务器上的项目
    CopyProject $cloneFolder\$projectName\'_site' $serverFolder\$projectName

    # 获取该项目指定类型的文件列表
    $fileList = GetFileList $serverFolder\$projectName '*.html' 

    # 遍历文件列表，替换耗时 js
    Replace $fileList $encoding
}

<#
# jslint-error-explanations 的网站已经不再开源，所以将最后开源的内容生成网站拷贝到服务器上即可（只需做一次）
# $gitUrl：API 文档的源码地址
# $cloneFolder：将 API 文档的源码 clone 到此文件夹下
# $serverFolder：API 文档在服务器上的文件夹
# $encoding：读写 API 文档源码的编码
#>
function GenerateJslintWebsite($gitUrl, $cloneFolder, $serverFolder, $encoding){
    if(!(Test-Path $cloneFolder)){
        mkdir $cloneFolder | Out-Null
    }

    if(!(Test-Path $serverFolder)){
        mkdir $serverFolder | Out-Null
    }

    # 从 git 地址中获取项目名称
    $projectName = GetDirectoryName $gitUrl

    # 克隆项目（已不再开源，clone 后要切换到“Remove Jekyll site”之前的那个提交）
    CloneProject $gitUrl $cloneFolder    

    # 生成静态网站，所以将最后开源的内容生成网站拷贝到服务器上即可
    Push-Location
    Set-Location $cloneFolder\$projectName
    CheckOut 'd7a70009bf2eac2b01675efa160028d0e4205e89'
    jekyll build
    Pop-Location

    # 更新服务器上的项目
    CopyProject $cloneFolder\$projectName\'_site' $serverFolder\$projectName

    # 获取该项目指定类型的文件列表
    $fileList = GetFileList $serverFolder\$projectName '*.html' 

    # 遍历文件列表，替换耗时 js
    Replace $fileList $encoding
}

<#
# 生成 html2jade 的网站
# $gitUrl：网站源码地址
# $serverFolder：网站源码在服务器上的文件夹
# $encoding：读写网站源码的编码
#>
function GenerateHtml2jadeWebsite($gitUrl, $serverFolder, $encoding){
    if(!(Test-Path $serverFolder)){
        mkdir $serverFolder | Out-Null
    }

    # 从 git 地址中获取项目名称
    $projectName = GetDirectoryName $gitUrl

    # 暂停服务
    StopWindowsService $projectName    

    # 克隆项目
    CloneProject $gitUrl $serverFolder

    # npm 安装
    Push-Location
    Set-Location $serverFolder\$projectName
    npm install
    Pop-Location

    # 获取该项目指定类型的文件列表
    $fileList = GetFileList $serverFolder\$projectName '*.html' 

    # 遍历文件列表，替换耗时 js
    Replace $fileList $encoding

    # 将 web.js 里的端口改成 8003
    $webJs = Get-ChildItem -LiteralPath $serverFolder\$projectName\'web.js' | %{$_.FullName}
    $fileContent = [System.IO.File]::ReadAllText($webJs, $encoding)
    $newContent = $fileContent -creplace '9999', '8003'
    [System.IO.File]::WriteAllText($webJs, $newContent, $encoding)

    # 启动服务
    StartWindowsService $projectName  
}

<#
# 生成 extjs 的 API 文档网站
# $docUrl：API 文档网站源码的下载地址
# $serverFolder：API 文档网站在服务器上的文件夹
# $encoding：读写网站源码的编码
#>
function GenerateExtjsWebsite($docUrl, $cloneFolder, $serverFolder, $encoding){
    if(!(Test-Path $cloneFolder)){
        mkdir $cloneFolder | Out-Null
    }

    if(!(Test-Path $serverFolder)){
        mkdir $serverFolder | Out-Null
    }

    # 获取文件名
    $projectName = 'extjs'
    if ($docUrl -cmatch '(?m)http://cdn\.sencha\.com/downloads/docs/(?<FILE_NAME>ext-docs-\w+\.\w+\.\w+)\.zip') {
        $docZipFullName = $Matches.FILE_NAME+'.zip'
        $docZipBaseName = $Matches.FILE_NAME
    } else {
        return
    }

    # 下载 API 文档网站源码
    if(!(Test-Path $cloneFolder\$docZipFullName)) {
        $dl = New-Object System.Net.WebClient
        $targetPath = $cloneFolder, $docZipFullName -join '\'
        $dl.DownloadFile($docUrl, $targetPath)
    }
        
    if(Test-Path $cloneFolder\$projectName){
        Remove-Item $cloneFolder\$projectName -Force -Recurse
    }
    # 解压
    ExtractZip $cloneFolder\$docZipFullName $cloneFolder\$projectName

    # 更新服务器上的项目（因为解压出来还有一层文件夹，所以是将这一层文件夹里面的东西拷到服务器上）
    $file = Get-ChildItem -LiteralPath $cloneFolder\$projectName | %{$_.BaseName}
    CopyProject $cloneFolder\$projectName\$file $serverFolder\$projectName

    # 获取该项目指定类型的文件列表
    $fileList = GetFileList $serverFolder\$projectName '*.html' 

    # 遍历文件列表，替换耗时 js
    Replace $fileList $encoding
}

#endregion


#region Variable 开始生成各个镜像站点

# 定义一些通用的变量
$currentPath = Split-Path -Parent $MyInvocation.MyCommand.Definition
$cloneFolder = $currentPath, 'clone' -join '\'
$serverFolder = $currentPath, 'server' -join '\'
$encoding = [System.Text.Encoding]::GetEncoding('iso-8859-1')

if(!(Test-Path $cloneFolder)){
   mkdir $cloneFolder | Out-Null
}

if(!(Test-Path $serverFolder)){
   mkdir $serverFolder | Out-Null
}

#region Variable 生成 AngularJs 的 API 文档
#$angularGit = 'git@github.com:angular-cn/ng-docs-en.git'
$angularGit = 'https://github.com/angular-cn/ng-docs-en.git'
GenerateWebsite $angularGit $cloneFolder $serverFolder $encoding
#endregion

#region Variable 生成 ionic 的 API 文档
#$ionicGit = 'git@github.com:driftyco/ionic-site.git'
$ionicGit = 'https://github.com/driftyco/ionic-site.git'
GenerateIonicWebsite $ionicGit $cloneFolder $serverFolder $encoding
#endregion

#region Variable 生成 JSLint ErrorExplanations 的文档
#$jsLintGit = 'git@github.com:jamesallardice/jslint-error-explanations.git'
$jsLintGit = 'https://github.com/jamesallardice/jslint-error-explanations.git'
GenerateJslintWebsite $jsLintGit $cloneFolder $serverFolder $encoding
#endregion

#region Variable 生成 html2jade 网站
#$html2jadeGit = 'git@github.com:aaronpowell/html2jade-website.git'
$html2jadeGit = 'https://github.com/aaronpowell/html2jade-website.git'
GenerateHtml2jadeWebsite $html2jadeGit $serverFolder $encoding
#endregion

#region Variable 生成 extjs 网站
$docUrl = 'http://cdn.sencha.com/downloads/docs/ext-docs-5.1.0.zip'
GenerateExtjsWebsite $docUrl $cloneFolder\'extjs' $serverFolder $encoding
#endregion

#endregion
```