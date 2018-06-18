---

copyright:
  years: 2014, 2018
lastupdated: "2018-05-16"

---
{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:pre: .pre}
 
# Configuración de {{site.data.keyword.blockstorageshort}} para la copia de seguridad con Plesk

Este artículo le ayudará a configurar {{site.data.keyword.blockstoragefull}} para sus copias de seguridad en Plesk. Suponemos que está disponible el acceso de SSH sudo o root y de Plesk a nivel administrador completo. Nuestras instrucciones se basan en un host CentOS7.

**Nota**: encontrará la documentación de Plesk sobre copia de seguridad y restauración [aquí](https://docs.plesk.com/en-US/12.5/administrator-guide/backing-up-and-restoration.59256/){:new_window}.

1. Conéctese al host a través de SSH.

2. Asegúrese de que un exista un destino de punto de montaje. <br />
   **Nota**: Plesk tiene dos opciones para almacenar copias de seguridad. Una opción es el almacenamiento interno de Plesk (almacenamiento de copia de almacenamiento en el servidor Plesk) y la otra es un almacenamiento FTP externo (almacenamiento de copia de seguridad en algún servidor externo en la web o en la red local). Normalmente en las cajas Plesk, las copias de seguridad internas se almacenan en `/var/lib/psa/dumps` y utilice `/tmp` como directorio temporal. En nuestro ejemplo, mantenemos el directorio temporal local, pero movemos el directorio de volcado al destino de STaaS (`/backup/psa/dumps`). No se necesitan credencial de usuario FTP.
   
3. Configure su {{site.data.keyword.blockstorageshort}} como se describe en [Conexión a los LUN de iSCSI de MPIO en Linux](accessing_block_storage_linux.html). Monte {{site.data.keyword.blockstorageshort}} en `/backup` y configure `/etc/fstab` para habilitar el montaje al arrancar.

4. **Opcional**: Copie las copias de seguridad existentes en el nuevo almacenamiento. Puede utilizar `rsync`:
   ```
   rsync -avz /var/lib/psa/dumps /backup/psa/dumps
   ```
   {: pre}
    
    **Nota**: este mandato transfiere sus datos comprimidos conservando todo lo posible, excepto los enlaces fijos. Proporciona información sobre los archivos que se están transfiriendo, además de un breve resumen final.
    
5. Edite `/etc/psa/psa.conf` para que apunte al valor de `DUMP_D` en el nuevo destino. 
    - Debería aparecer como: `DUMP_D /backup/psa/dumps`. 

6. **Opcional**: en función de sus necesidades específicas, elimine el almacenamiento antiguo del servidor y cancele desde la cuenta.


