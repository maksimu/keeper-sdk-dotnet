
1. Create new Azure Function App
    - Azure Portal -> `Function App` -> `+ Add`<br /></br>
  
    - Resource Group: [Select]
    - Function App name: [Type]
    - Publish: [Code]
    - Runtime stack: PowerShell Core
    - Version: 7.0
    - Region: [Select]<br /></br>
  
    - Storage account: [Select]
    - OS: Windows (the only option)
    - Plan type: Consumption (Serverless)<br /></br>
  
    - Enable Application Insights: Yes
    - Application Insights: [Select]
   
2. Create new Function:
    - Functions -> `+ Add`
    - Development environment: Develop in portal
    - Template: HTTP trigger
    - New Function: [type name]
    - Authorization level: [Select]
   
3. Upload module

    - Open "Kudu" by going to the Azure function that was just created -> Advanced Tools -> "Go ->" -> "Debug Console" (Top navigation) -> "PowerShell"
    - Navigate to `site/wwwroot/[FUNC NAME]`
    - Create new folder: `modules`
    - Upload SDK related files
        - `KeeperSdk.dll` (compiled w/ Protobuf version 3.11.4)
        - `BouncyCastle.Crypto.dll`
        - `System.Buffers.dll`
        - `System.Memory.dll`
        - `System.Runtime.CompilerServices.Unsafe.dll`
    - Upload Power commander related files:
        - `AuthCommandsDaemon.ps1`
        - `AuthCommands.ps1`
        - `Library.format.ps1xml`
        - `Library.types.ps1xml`
        - `PowerCommander.format.ps1xml`
        - `PowerCommander.psd1`
        - `PowerCommander.psm1`
        - `PowerCommander.pssproj`
        - `PowerCommander.tests.ps1`
        - `PowerCommander.types.ps1xml`
        - `RecordCommands.ps1`
        - `SharedFolderCommands.ps1`
        - `VaultCommands.ps1`
   
4. Generate and upload config.json file
      - Using .NET Commander login with the account to be used to login programmatically. Config file was generated under `~/.keeper` folder<br />
        _Note: Download latest .NET Commander (under Assets -> Commander.zip) from [HERE](https://github.com/Keeper-Security/keeper-sdk-dotnet/releases) and execute `Commander.exe` in Command Prompt._
      - Enable persistent login by running:
        1. `this-device register`
        2. `this-device persistent_login on`
        3. `this-device ip_disable_auto_approve on`
      - Upload config.json file to Azure function by opening Kudu, navigating to `site/wwwroot/` and creating folder `.keeper` and then upload file there
   
5. Add/Modify code to the Function.
    - Open function's code and add following code

    Example code that loads module and uses it by authenticating and connecting to Keeper, then retrieves all records that belong to the user:
    
   ```powershell
    #Check for PowerCommander module
    if (-not (Get-Module -Name "PowerCommander"))
       {
       Write-Warning "PowerCommander not installed. Attempting to install..."
       Import-Module "C:\home\site\wwwroot\HttpTriggerThree\modules\PowerCommander.psd1" -Force
       # Write error message and quick
       # Write-Error "PowerCommander not installed" -ErrorAction Stop
    }
    else
    {
        Write-Output "PowerCommander installed";
        $(Get-Module -ListAvailable | where Name -EQ "PowerCommander" | Select-Object Name, Path);
    }
    
    # Connect to Keeper
    kcp
    
    # Retrieve records
    $records = kr
    ```
