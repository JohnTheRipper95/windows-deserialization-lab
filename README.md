# W10-YSoSerial

VM Windows 10 local para pruebas de penetracion relacionadas con
deserializacion y uso de `ysoserial.net`.

La VM usa Vagrant y VirtualBox. Ansible administra Windows mediante WinRM y
despliega la distribucion precompilada incluida en `files/ysoserial.zip`; no
instala Visual Studio, NuGet ni MSBuild.

## Requisitos

- Vagrant
- VirtualBox
- Ansible con la coleccion `ansible.windows`
- Un host Linux con soporte para WinRM desde Ansible

## Uso

Desde la raiz del repositorio:

```bash
vagrant up
```

El playbook extrae ysoserial.net en `C:\opt\ysoserial.net` y ejecuta
`ysoserial.exe -h` como comprobacion basica. Tambien agrega
`C:\opt\ysoserial.net\Release` al `PATH` del sistema, por lo que se puede
invocar `ysoserial.exe` desde cualquier consola nueva de Windows.

Para volver a ejecutar el provisionamiento:

```bash
vagrant provision
```

## Alcance y aislamiento

Este repositorio esta pensado para un laboratorio local y desechable. Las
credenciales `vagrant/vagrant`, el transporte WinRM sobre HTTP y la validacion
de certificado deshabilitada solo son aceptables para esta VM local.

- Mantener el puerto WinRM publicado unicamente en `127.0.0.1`.
- No usar una interfaz de red bridged salvo que sea estrictamente necesario.
- No exponer la VM ni sus servicios vulnerables a Internet o a redes no
  controladas.
- Crear un snapshot limpio antes de las pruebas y restaurarlo despues de
  ejecutar payloads destructivos.
- No reutilizar las credenciales de laboratorio en otros sistemas.

El puerto local `55985` se usa para acceder al WinRM de la VM a traves de
Vagrant. La configuracion esta en `inventory/hosts` y `Vagrantfile`.

## Contenido precompilado

El ZIP local contiene `Release/ysoserial.exe` y sus dependencias. Si se
reemplaza el ZIP, debe conservarse esa ruta o actualizar `executable_path` en
`playbook.yml`.
