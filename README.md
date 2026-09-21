# W10-YSoSerial

Local Windows 10 VM for penetration testing exercises focused on .NET
serialization and `ysoserial.net`.

The VM uses Vagrant and VirtualBox. Vagrant uploads the precompiled
distribution from `files/ysoserial.zip` to the Windows guest, and Ansible
provisions it over WinRM. The setup does not install Visual Studio, NuGet, or
MSBuild. Provisioning preserves the Windows hostname from the base box.

The precompiled tool is based on the upstream
[pwntester/ysoserial.net](https://github.com/pwntester/ysoserial.net)
repository. Refer to that project for its source code, documentation, and
licensing details.

## Attribution

This repository does not claim ownership of `ysoserial.net`. It provides
automation and a local Windows lab environment for deploying and using the
precompiled tool.

`ysoserial.net` is maintained by its original authors. See the upstream
repository for authorship, licensing, and project terms:

https://github.com/pwntester/ysoserial.net

## Licensing

The automation, configuration, and documentation created for this repository
are released under the MIT License. See [LICENSE](LICENSE).

The precompiled `ysoserial.net` distribution and bundled dependencies retain
their original licenses. See [THIRD-PARTY-NOTICES.md](THIRD-PARTY-NOTICES.md)
and the upstream project for details.

## Requirements

- Vagrant
- VirtualBox
- Ansible with the `ansible.windows` collection
- A Linux host configured for WinRM with Ansible

## Usage

From the repository root:

```bash
vagrant up
```

The Windows VM login credentials are:

```text
Username: vagrant
Password: vagrant
```

Provisioning may take several minutes while Windows boots and the files are
uploaded. Please wait for the process to complete before interrupting it or
opening a new terminal to test the VM.

The playbook extracts `ysoserial.net` to `C:\opt\ysoserial.net`, adds
`C:\opt\ysoserial.net\Release` to the system `PATH`, and runs
`ysoserial.exe -h` as a basic verification step. Open a new Windows console
after provisioning so it receives the updated `PATH`.

To run provisioning again:

```bash
vagrant provision
```

After provisioning, `ysoserial.exe` can be invoked from any new Windows
console.

If an already open PowerShell session cannot find the command, close it and
open a new one. Existing processes retain the environment they inherited when
they started. To refresh the current session without reopening it, run:

```powershell
$env:Path = [Environment]::GetEnvironmentVariable("Path", "Machine")
```

You can then verify the command with:

```powershell
Get-Command ysoserial.exe
```

or CMD:

```cmd
ysoserial -h
```

## Scope and isolation

This repository is intended for a local, disposable lab. The
`vagrant/vagrant` credentials, WinRM over HTTP, and disabled certificate
validation are only acceptable for this local VM.

- Keep the WinRM port bound to `127.0.0.1`.
- Do not use a bridged network interface unless it is strictly necessary.
- Do not expose the VM or its vulnerable services to the Internet or
  uncontrolled networks.
- Create a clean snapshot before testing and restore it afterward when running
  destructive payloads.
- Do not reuse the lab credentials on other systems.

Local port `55985` provides access to the VM's WinRM endpoint through Vagrant.
The connection settings are defined in `inventory/hosts` and `Vagrantfile`.

## Precompiled contents

The local ZIP contains `Release/ysoserial.exe` and its dependencies. Vagrant
uploads it to `C:\Windows\Temp\ysoserial.zip` before Ansible runs. If the ZIP
is replaced, preserve the `Release/ysoserial.exe` path or update
`executable_path` and `executable_dir` in `playbook.yml`.
