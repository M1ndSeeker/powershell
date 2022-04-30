# powershell

Чтобы узнать, где должен располагаться файл, вы можете применить встроенную переменную $profile для возвращения пути и имени, используемого для файла. Для этого просто запустите команду:

$profile
Переменная возвращает полное имя файла, например C:UsersAdministratorDocumentsWindowsPowerShellMicrosoft.PowerShell_profile.ps1. Тем не менее, основываясь лишь на том, что переменная $profile указывает на файл, нельзя сделать вывод о существовании файла. Поэтому следующим шагом будет запуск команды:

Test-Path $profile
Команда Test-Path проверяет наличие файла. Если он существует, команда возвращает True; если нет, возвращается False. В последнем случае вам нужно выполнить следующую команду, прежде чем вы предпримите какие-либо другие действия:

New-Item -path $profile -type file -force
Команда New-Item создает файл на основе аргумента, переданного в параметре -path, в данном случае это переменная $profile. Параметр -type указывает, что создаваемый элемент — не что иное, как файл (как и определено ключевым словом «файл»). Параметр -force предписывает команде создать файл.

После того, как файл создан, вы можете повторить команду Test-Path и убедиться, что o она возвращает True. Готовый файл можно редактировать с помощью команды:

notepad $profile

##code

function prompt {

    #Assign Windows Title Text
    $host.ui.RawUI.WindowTitle = "Current Folder: $pwd"

    #Configure current user, current folder and date outputs
    $CmdPromptCurrentFolder = Split-Path -Path $pwd -Leaf
    $CmdPromptUser = [Security.Principal.WindowsIdentity]::GetCurrent();
    $Date = Get-Date -Format 'dddd hh:mm:ss tt'

    # Test for Admin / Elevated
    $IsAdmin = (New-Object Security.Principal.WindowsPrincipal ([Security.Principal.WindowsIdentity]::GetCurrent())).IsInRole([Security.Principal.WindowsBuiltinRole]::Administrator)

    #Calculate execution time of last cmd and convert to milliseconds, seconds or minutes
    $LastCommand = Get-History -Count 1
    if ($lastCommand) { $RunTime = ($lastCommand.EndExecutionTime - $lastCommand.StartExecutionTime).TotalSeconds }

    if ($RunTime -ge 60) {
        $ts = [timespan]::fromseconds($RunTime)
        $min, $sec = ($ts.ToString("mm\:ss")).Split(":")
        $ElapsedTime = -join ($min, " min ", $sec, " sec")
    }
    else {
        $ElapsedTime = [math]::Round(($RunTime), 2)
        $ElapsedTime = -join (($ElapsedTime.ToString()), " sec")
    }

    #Decorate the CMD Prompt
    Write-Host ""
    Write-host ($(if ($IsAdmin) { 'Elevated ' } else { '' })) -BackgroundColor DarkRed -ForegroundColor White -NoNewline
    Write-Host " USER:$($CmdPromptUser.Name.split("\")[1]) " -BackgroundColor DarkBlue -ForegroundColor White -NoNewline
    If ($CmdPromptCurrentFolder -like "*:*")
        {Write-Host " $CmdPromptCurrentFolder "  -ForegroundColor White -BackgroundColor DarkGray -NoNewline}
        else {Write-Host ".\$CmdPromptCurrentFolder\ "  -ForegroundColor White -BackgroundColor DarkGray -NoNewline}

    Write-Host " $date " -ForegroundColor White
    Write-Host "[$elapsedTime] " -NoNewline -ForegroundColor Green
    return "> "
} #end prompt function
