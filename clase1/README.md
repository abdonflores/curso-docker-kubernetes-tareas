# Tareas - Curso Docker & Kubernetes

**Autor**: Luis Abdon Flores  
**Curso**: [Docker & Kubernetes - i-Quattro](https://iquattrogroup.com/mod/url/view.php?id=1251)

---

## 📚 Índice

- [Clase 1](clase1/)  (https://iquattrogroup.com/mod/url/view.php?id=1251)


 ## tarea:

 #Nombre de la aplicación Opción 1: Apache HTTP Server (httpd)
 
 Comandos ejecutados 
   
a.   docker run -d --name mi-apache -p 8081:80 httpd
 > Ejecuta el contenedor Apache en segundo plano y --name mi-apache: Asigna un nombre al contenedor, -d: Ejecuta el contenedor en segundo plano. p 8081:80: Mapea el puerto 8081 del host al puerto 80 dentro del contenedor. Es la imagen oficial de Apache HTTP Server desde Docker Hub

b. docker ps
 > Verifica  que el contenedor está corriendo 

c. Verificar en el navegador

## Lo aprendido:

> En este ejercicio aprendí cómo desplegar un servidor web básico con Docker usando una imagen oficial. La sintaxis de docker run es muy intuitiva, y el mapeo de puertos (-p) me permitió acceder al servicio desde mi máquina local. La mayor dificultad fue entender que el contenedor debe estar en segundo plano para poder usarlo mientras trabajo en otros comandos, pero con -d se solucionó fácilmente. Además, aprender a limpiar correctamente los recursos con stop y rm es clave para mantener el sistema organizado. 

