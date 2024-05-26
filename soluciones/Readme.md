El ejercicio esta compuesto por 3 partes, una base de datos mysql, un laravel y  un phpmyadmin

Todos los componentes se despliegan en un namespace llamado laravel

Laravel:
Se utiliza la imagen de Docker bitnami/laravel  en su versión 11.0.8 para desplegar la aplicación Laravel.
Se configuran límites y requests de CPU y memoria para el pod de Laravel.
Se configuran unas sondas readinessProbe y un livenessProbe para el pod de Laravel.
Se crea un  un volumen persistente (pvc) para Laravel.
Se configura un Ingress para acceder a la aplicación Laravel desde un navegador mediante http://laravel.com:8000/laravel 


Base de datos Mysql:
Se Utiliza la imagen oficial de Docker mysql en su versión 8.4.0 para desplegar la base de datos MySQL.
Se configuran límites y requests de CPU y memoria para el pod de MySQL.
Se crea un volumen persistente para la base de datos MySQL.
En lugar de un Deployment, se utiliza un StatefulSet (o similar) para desplegar la base de datos MySQL, ya que es más adecuado para mantener el estado de la base de datos

Phpmyadmin:
Se utiliza la imagen oficial de Docker phpmyadmin para desplegar phpMyAdmin.
Se configura un Ingress para acceder a phpMyAdmin desde un navegador. http://laravel.com:8000/phpmyadmin
Se configura phpMyAdmin para requerir autenticación (quitando del deployemnt el usuario y la contraseña).


Chart:
Todas las configuraciones de los componentes están en el archivo values.yaml del Helm Chart, que por cierto engloba todo el funcionamiento del proyecto . Esto incluye el namespace, las versiones de las imágenes, las configuraciones de Laravel, MySQL y phpMyAdmin, los recursos de CPU y memoria, la capacidad de los volúmenes persistentes, las configuraciones de los Ingress, y las contraseñas.
En cuanto a su contenido, ha quedado configurado dentro del :
    Laravel:
        Se despliega una réplica de Laravel.
        Se utiliza la versión 11.0.8 de la imagen de Laravel.
        Se configuran los límites y requests de CPU y memoria para Laravel.
        Se crea un volumen persistente de 1Gi para Laravel.

    MySQL:
        Se despliega una réplica de MySQL.
        Se utiliza la versión 8.4.0 de la imagen de MySQL.
        Se configuran los límites y requests de CPU y memoria para MySQL.
        Se crea un volumen persistente de 1Gi para MySQL.
        Se configuran el nombre de la base de datos, el usuario y las contraseñas.

    phpMyAdmin:
        Se despliega una réplica de phpMyAdmin.
        Se utiliza la versión 5.2.1 de la imagen de phpMyAdmin.
        Se puede configurar si se despliega o no phpMyAdmin.

    Ingress:
        Se configura un Ingress con el nombre de host laravel.com y el path /laravel.
        Se utiliza la clase de Ingress nginx.

    Otros:
        Se configuran las sondas de liveness y readiness.
        Se configuran los recursos y las políticas de pull de la imagen.
        Se configuran las opciones de autoscaling.
        Se configuran los volúmenes y los montajes de volúmenes adicionales.
        Se configuran el nodeSelector, las tolerancias y la afinidad.

Para lanzarlo :
helm install app-prueba . --namespace laravel
minikube service ingress-nginx-controller -n ingress-nginx
kubectl port-forward phpmyadmin-deployment-6497dff464-fg5qr  8000:80 -n laravel
kubectl port-forward laravel-deployment-6fc6b8f798-ptcmm 8000:8000 -n laravel