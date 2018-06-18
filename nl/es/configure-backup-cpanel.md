---

copyright:
  years: 2014, 2018
lastupdated: "2018-05-16"

---
{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
 
# Configuración de {{site.data.keyword.blockstorageshort}} para la copia de seguridad con cPanel

Este artículo le ayudará a configurar sus copias de seguridad en cPanel para que se almacenen en {{site.data.keyword.blockstoragefull}}. Suponemos que está disponible el acceso de SSH sudo o root y de WebHost Manager (WHM) completo. Nuestras instrucciones se basan en un host **CentOS 7**.

**Nota**: en la documentación de cPanel encontrará instrucciones sobre **Cómo configurar el directorio de copias de seguridad ** [aquí](https://docs.cpanel.net/display/68Docs/Backup+Configuration#BackupConfiguration-ConfigureBackupDirectory){:new_window}.

1. Conéctese al host a través de SSH.

2. Asegúrese de que un exista un destino de punto de montaje. <br />
   **Nota**: de forma predeterminada, el sistema cPanel guarda los archivos de copia de seguridad en local, en el directorio `/backup`. Para la finalidad de este documento, suponemos que `/backup` ya existe y contiene copias de seguridad, de modo que utilizaremos `/backup2` como el nuevo punto de montaje.
   
3. Configure su {{site.data.keyword.blockstorageshort}} como se describe en [Conexión a los LUN de iSCSI de MPIO en Linux](accessing_block_storage_linux.html). Asegúrese de montarlo en `/backup2` y configurarlo en `/etc/fstab` para habilitar el montaje al arrancar.

4. **Opcional**: Copie las copias de seguridad existentes en el nuevo almacenamiento. Puede utilizar `rsync`:
   ```
   rsync -azv /backup/* /backup2/
   ```
   {: pre}
    
    **Nota**: este mandato transfiere sus datos comprimidos conservando todo lo posible, excepto los enlaces fijos. Proporciona información sobre los archivos que se están transfiriendo, además de un breve resumen final.
    
5. Inicie sesión en WebHost Manager y vaya a la configuración de la copia de seguridad pulsando **Inicio** > **Copia de seguridad** > **Configuración de copia de seguridad**.

6. Edite la configuración para guardar las copias seguridad en el nuevo punto de montaje. 
    - Cambie el directorio de copia de seguridad predeterminado especificando la vía de acceso absoluta a la nueva ubicación en su lugar del directorio /backup/. 
    - Seleccione **Habilitar el montaje de una unidad de copia de seguridad**. Este valor hace que el proceso de configuración de copia de seguridad compruebe el archivo `/etc/fstab` para el montaje de la copia de seguridad (`/backup2`). <br /> **Nota**: si existe un montaje con el mismo nombre que el directorio intermedio, el proceso de configuración de copia de seguridad monta la unidad y realiza la copia de seguridad de la información en el montaje. Cuando el proceso de copia de seguridad finaliza, desmonta la unidad. 

7. Aplique los cambios pulsando **Guardar configuración** en la parte inferior de la interfaz **Configuración de copia de seguridad**.

8. **Opcional**: en función de sus necesidades específicas, elimine el almacenamiento antiguo del servidor y cancele desde la cuenta.

