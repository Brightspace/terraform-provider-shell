# Shell Provider

`shell` is a special provider that exists to provide an interface between Terraform and external scripts.

Using this provider it is possible to write scripts that can participate in the Terraform workflow by implementing a specific protocol.

This provider is intended to be used for situations where implementing a terraform provider for an interface would be prohibitive. As we are working with scripts, trade-offs had to be made to ensure consistency when running each of these examples. 

## Example Usage

```terraform
# Configure the Shell provider
provider "shell" {
  working_directory = "/tmp"

  variables = {
    USERNAME = "MyUser"
    PASSWORD = "Password1"
  }
}

# Map to a script
resource "shell" "run_script" {
  # ...
}
```

And a simple powershell can be written like so, where the target output is specified by the environment variable `TF_DATA_FILE`.

```powershell
param(
    [String]$Action
)

Write-Output "$($env:TF_ID): ID Passed in."
$Result = Get-FromMyService | ConvertTo-JSON
switch($Action) {
   "create" { Set-Content -Path $env:TF_DATA_FILE -Value $Result; break}
   "read" { Set-Content -Path $env:TF_DATA_FILE -Value $Result; break}
   "delete" { Set-Content -Path $env:TF_DATA_FILE -Value ""; break}
   "update" { Set-Content -Path $env:TF_DATA_FILE -Value $Result; break}
}
```

## Argument Reference

The following arguments are supported in the `provider` block:

- `working_directory` - (Optional) The direction where the scripts will run from. It will be defaulted to a temporary location.
- `variables` - (Optional) Environment variables that will be passed down to all scripts using this provider.
