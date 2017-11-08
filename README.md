### Examen 2 Solución
**Universidad ICESI**  
**Curso:** Sistemas Operativos  
**Docente:** Daniel Barragán C.  
**Tema:** Namespaces, CGroups, LXC  
**Correo:** daniel.barragan at correo.icesi.edu.co

**Nombre:** Carlos Andrés Afanador Cabal  
**Código:** A00054652  
**Correo:** carlosafanador97 at gmail  
**URL Git:** https://github.com/yosoyafa/so-exam2   

### Punto 3

 Inicialmente se va a la configuración de la maquina virtual para asignarle solamente un núcleo de CPU y poder cumplir con los requerimientos de la actividad.  
 
  ![][1]
  
  Ahora se crean los scripts a ejecutar, xd.sh y baia.sh :
  
   ![][2]
   
   A estos archivos se les debe dar permisos de ejecución, pero para evitar cualquier complicación, se les otorgaron todos los permisos:
   ```
# chmod 777 xd.sh
# chmod 777 baia.sh
```
   
   Después se deben crear los servicios correspondientes a cada script, y para llevar esto a cabo esto, nos debemos dirigir al directorio */etc/systemd/system*:
   
```
# cd /etc/systemd/system
```
Se crean los servicios s1.service y s2.service, dándole a cada uno todos los permisos:

```
# touch s1.service
# touch s2.service
# chmod 777 s1.service
# chmod 777 s2.service
```
Se entra a editar cada archivo con el comando _vi_, asignándoles la ruta del script a ejecutar (ExecStart=/root/p2/nombreScript.sh) y el porcentaje de CPU determinado (CPUQuota=50%):

   ![][3]
   
Ahora bien, para que los servicios puedan funcionar, se debe habilitar cada uno:
   ```
# systemctl enable s1.service
# systemctl enable s2.service
```
También se debe recargar el servicio de systemctl:

   ```
# systemctl daemon-reload
```

Y por último, iniciar cada servicio:
   ```
# systemctl start s1.service
# systemctl start s2.service
```

Ya con ambos servicios corriendo, podemos observar su comportamiento mediante el comando _top_:

   ![][4]

Evidentemente, la capacidad de la CPU está siendo repartida 50%-50% (aprox.) entre los dos servicios. Ahora se procede a detener uno de los dos servicios, y se puede obsevar que el proceso que aun vive, sigue trabajando con el 50% de la capacidad de la CPU:


   ![][5]
   
   
### Punto 4

Para este punto se usan los mismos scripts usados anteriormente, pero se debe modificar los archivos .service, cambiando CPUQuota por CPUShares:

   ![][6]
   
Al servicio s1.service se le asigno un CPUShares de 750 (equivalente al 75% de la CPU), y al servicio s2.service se le asigno un CPUShares de 250 (equivalente al 25% de la CPU): 

Para poner en funcionamiento estas nuevas versiones de los servicios se debe volver a acer todo el proceso:

   ```
# systemctl enable s1.service
# systemctl enable s1.service
# systemctl daemon-reload
# systemctl start s1.service
# systemctl start s2.service
```

Ya con ambos servicios corriendo, podemos observar su comportamiento mediante el comando _top_:


   ![][7]
   
Evidentemente, el servicio al que se le asigno el CPUShares de 750, está usando el 75% (aprox.) de la CPU, lo cual sucede de manera analoga con el servicio al cual se le asigno el CPUShares de 250 (25%). Ahora bien, si se cancela uno de los dos procesos, el otro termina usando toda la capacidad de la CPU:

   ![][8]
   
   
### Punto 4

**CPUQuota:** restringe el tiempo de procesamiento de la CPU al porcentaje asignado (ej.: CPUQuota=50%), incluso si el resto del procesador no está en uso.

**CPUShares:** refiere a la cantidad de 

En conclusión, CPUQuota es ideal para restringir los recursos de la CPU dado el caso en el que exista algún proceso que esté consumiendo toda la capacidad de procesamiento, y con el uso de CPUQuota los demás procesos podrán desarrollarse normalmente. CPUShares 

### Referencias
··*https://sysdig.com/blog/monitoring-greedy-containers-part-1/
··*https://www.certdepot.net/rhel7-get-started-cgroups/
   
[1]: images/conf.png
[2]: images/1-scripts.png
[3]: images/1-servicesCPUQuota.png
[4]: images/1-top2procs.png
[5]: images/1-top1proc.png
[6]: images/2-servicesCPUShares.png
[7]: images/2-top2procs.png
[8]: images/2-top1proc.png
