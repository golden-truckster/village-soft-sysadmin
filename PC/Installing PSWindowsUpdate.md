
Installing PSWindowsUpdate

First you will need to install the PSWindowsUpdate module. 
To do so, launch PowerShell which is preferably PowerShell 7, and run the following command to install and then import the module.


#Либо вы можете разрешить запускать команды модуля в текущей сессии PowerShell:
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process


[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12


#Импортируйте модуль в сессию PowerShell:
Install-Module -Name PSWindowsUpdate


#
Import-Module -Name PSWindowsUpdate




 
#
Команда должна вывести список обновлений, которые нужно установить на вашем компьютере.
Get-WUList

# 
Windows загрузит все доступные патчи сервера обновлений
Get-WindowsUpdate -Download -AcceptAll

#
Чтобы автоматически скачать и установить все доступные обновления для вашей версии Windows с серверов Windows Update (вместо локального WSUS), выполните:

Install-WindowsUpdate -MicrosoftUpdate -AcceptAll -AutoReboot

Ключ AcceptAll включает одобрение установки для всех пакетов, а AutoReboot разрешает автоматическую перезагрузку Windows после завершения установки обновлений.
Также можно использовать следующе параметры:

    IgnoreReboot – запретить автоматическую перезагрузку;
    ScheduleReboot – задать точное время перезагрузки компьютера.

#
Проверить, нужна ли перезагрузка компьютеру после установки обновления (атрибуты RebootRequired и RebootScheduled):

Get-WURebootStatus 

Get-WindowsUpdate -MicrosoftUpdate


Get-WUServiceManager


Add-WUServiceManager -ServiceID "7971f918-a847-4430-9279-4a52d1efe18d" -AddServiceFlag 7




Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force


Можно установить только конкретные обновления по номерам KB:

Get-WindowsUpdate -KBArticleID KB2267602, KB4533002 -Install

Install-WindowsUpdate установка обновлений windows с помощью powershell

Если вы хотите пропустить некоторые обновления при установке, выполните:

Install-WindowsUpdate -NotCategory "Drivers" -NotTitle OneDrive -NotKBArticleID KB4011670 -AcceptAll -IgnoreReboot

Проверить, нужна ли перезагрузка компьютеру после установки обновления (атрибуты RebootRequired и RebootScheduled):

Get-WURebootStatus

Get-WURebootStatus нужна ли перезагрузка Windows после установки обновлений
>Просмотр истории установленных обновлений в Windows

С помощью команды Get-WUHistory вы можете получить список обновлений, установленных на компьютере ранее автоматически или вручную.

Get-WUHistory - история установки обновлений

Можно получить информацию о дате установки конкретного обновления:

Get-WUHistory| Where-Object {$_.Title -match "KB4517389"} | Select-Object *|ft

Get-WUHistory найти установленные обновления

Вывести даты последнего сканирования и установки обновлении на компьютере:

Get-WULastResults |select LastSearchSuccessDate, LastInstallationSuccessDate

Get-WULastResults время последней установки обновлений в Windows
Удаление обновлений в Windows с помощью PowerShell

Для корректного удаления обновления Windows используется командлет Remove-WindowsUpdate. Вам достаточно указать номер KB в качестве аргумента параметра KBArticleID.

Remove-WindowsUpdate -KBArticleID KB4011634
Скрыть ненужные обновления Windows с помощью PowerShell

Вы можете скрыть определенные обновления, чтобы они никогда не устанавливались службой обновлений Windows Update на вашем компьютер (чаще всего скрывают обновления драйверов). Например, чтобы скрыть обновления KB2538243 и KB4524570, выполните такие команды:

$HideList = "KB2538243", "KB4524570"
Get-WindowsUpdate -KBArticleID $HideList -Hide

или используйте alias:

Hide-WindowsUpdate -KBArticleID $HideList -Verbose

Hide-WindowsUpdate - скрыть обновление, запретить установку

Теперь при следующем сканировании обновлений с помощью команды Get-WindowsUpdate скрытые обновления не будут отображаться в списке доступных для установки.

Вывести список скрытых обновлений:

Get-WindowsUpdate –IsHidden

Обратите внимание, что в колонке Status у скрытых обновлений появился атрибут H (Hidden).

Get-WindowsUpdate –IsHidden отобразить скрытые обновления windows

Отменить скрытие обновлений можно так:

Get-WindowsUpdate -KBArticleID $HideList -WithHidden -Hide:$false

или так:

Show-WindowsUpdate -KBArticleID $HideList 


MUP-DC1      -------    KB4103720    1GB Накопительное обновление для Windows Server 2016 для систем на базе процесс
MUP-DC1      -D-----    KB890830    66MB Средство удаления вредоносных программ для платформы x64: v5.123 (KB890830)
!!!MUP-DC1      -D-----    KB5037016   12MB Обновление служебного стека для Windows Server 2016 для систем на базе проц
MUP-DC1      -D-----    KB5036609   56MB 2024-04 Накопительное обновление .NET Framework 4.8 Windows Server 2016 для
MUP-DC1      -------    KB2267602  984MB Обновление механизма обнаружения угроз для Microsoft Defender Antivirus — K

#Get-WindowsUpdate -KBArticleID KB4103720 -Install
#Get-WindowsUpdate -KBArticleID KB5036609 -Install
#Get-WindowsUpdate -KBArticleID KB890830, KB2267602 -Install
Remove-WindowsUpdate -KBArticleID KB5037016
$HideList = "KB5037016"
Get-WindowsUpdate -KBArticleID $HideList -Hide
Hide-WindowsUpdate -KBArticleID $HideList -Verbose
Hide-WindowsUpdate

Get-WindowsUpdate –IsHidden