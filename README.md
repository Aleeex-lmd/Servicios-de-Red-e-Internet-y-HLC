# **Servicios de Red e Internet + HLC**

## **Unidad 6: Protocolo DNS**

### **Teoría**
- [**Teoria Josedomingo**](https://github.com/Aleeex-lmd/curso_kubernetes_ies.git)

### **Ejercicios**
- **Ejercicio 1**: Resolución de nombres de dominios en sistemas Linux  
  Practicar la resolución de nombres utilizando herramientas nativas de Linux.  
  - [Ver ejercicio 1](./Unidad-6:Protocolo-DNS/enunciados/e_ejercicio1.md)  

- **Ejercicio 2**: Consultas DNS con `dig`  
  Uso de la herramienta `dig` para realizar consultas DNS detalladas.
  - [Ver ejercicio 1](./Unidad-6:Protocolo-DNS/enunciados/e_ejercicio2.md)  

### **Servidor DNS bind9**
- **Taller 1**: Instalación y configuración del servidor bind9 en nuestra red local  
  Instrucciones paso a paso para configurar un servidor DNS usando `bind9`.
  - [Ver enunciado taller 1](./Unidad-6:Protocolo-DNS/enunciados/e_taller1.md)
  - [Ver taller 1 resuelto](./Unidad-6:Protocolo-DNS/enunciados_resueltos/s_taller1.txt)
  
- **Taller 2**: Instalación y configuración de un servidor DNS esclavo  
  Explicación de la configuración de un servidor DNS esclavo para replicar la información del servidor primario.
  - [Ver enunciado taller 2](./Unidad-6:Protocolo-DNS/enunciados/e_taller2.md)
  - [Ver taller 2 resuelto](./Unidad-6:Protocolo-DNS/enunciados_resueltos/s_taller2.txt)

- **Taller 3**: Delegación de subdominios con bind9  
  Establecer subdominios y delegar su gestión a servidores distintos.
  - [Ver enuncido taller 3](./Unidad-6:Protocolo-DNS/enunciados/e_taller3.md)
  - [Ver taller 3 resuelto](./Unidad-6:Protocolo-DNS/enunciados_resueltos/s_taller3.txt)

- **Taller 4**: Implementación de un DNS dinámico  
  Configuración de DNS dinámico para cambiar automáticamente las direcciones IP asociadas a un nombre de dominio.
  - [Ver enunciado taller 4](./Unidad-6:Protocolo-DNS/enunciados/e_taller4.md)
  - [Ver taller 4 resuelto](./Unidad-6:Protocolo-DNS/enunciados_resueltos/s_taller4.txt)

### **Prácticas**
- **Práctica (1 / 2)**: Servidores Web, Base de Datos y DNS en nuestro escenario de OpenStack  
  Desplegar servidores de DNS junto con servidores web y bases de datos en un entorno de OpenStack.
  - [Ver eunciado practica 1](./Unidad-6:Protocolo-DNS/enunciados/e_practica1.md)
  - [Ver resolución practica](./Unidad-6:Protocolo-DNS/enunciados_resueltos/s_practica1.md)
  - [Ver practica resualta](./Unidad-6:Protocolo-DNS/enunciados_resueltos/s_practica1.txt)

- **Práctica (2 / 2)**: Servidores Web, Base de Datos y DNS en nuestro escenario de OpenStack  
  Continuación de la práctica anterior con ajustes y configuraciones avanzadas.
  - [Ver enunciado practica 2](./Unidad-6:Protocolo-DNS/enunciados/e_practica3.md)
  - [Ver resolución practica](./Unidad-6:Protocolo-DNS/enunciados_resueltos/s_practica2.md)
  - [Ver practica resualta](./Unidad-6:Protocolo-DNS/enunciados_resueltos/s_practica2.txt)

---

## **Unidad 7: Servidor de Correo Electrónico**

### **Teoría**
- [**Teoria Josedomingo**](https://github.com/Aleeex-lmd/curso_correo_electronico_ies.git)

### **Talleres**
- **Taller 1**: Servidor de correo en los servidores de clase  
  Instalación y configuración básica de un servidor de correo en un entorno de clase.

- **Taller 2**: Servidores satélites, alias y redirecciones  
  Configuración de servidores de correo satélites, gestión de alias y redirección de correos.

### **Prácticas**
- **Práctica (1 / 2)**: Instalación y configuración de un servidor de correos en el VPS  
  Implementación práctica de un servidor de correo en un servidor privado virtual (VPS).

- **Práctica (2 / 2)**: Instalación y configuración de un servidor de correos en el VPS  
  Continuación de la práctica anterior, abordando configuraciones avanzadas de seguridad y optimización.

---

## **Unidad 8: Kubernetes**

### **Teoría**
- [**Teoria Josedomingo**](https://github.com/Aleeex-lmd/curso_kubernetes_ies.git)

### **Ejercicios**
- **Ejercicio 1**: Instalación y configuración de minikube y kubectl  
  Paso a paso para instalar y configurar Minikube y Kubectl para gestionar clusters de Kubernetes locales.
  - [Ver enunciado ejercicio 1](./Unidad-8:Kubernetes/enunciados/e_ejercicio1.md)

- **Ejercicio 2**: Trabajando con un Pod multicontenedor (VOLUNTARIO)  
  Implementación de un pod que contenga múltiples contenedores, permitiendo la comunicación entre ellos.
  - [Ver enunciado ejercicio 2](./Unidad-8:Kubernetes/enunciados/e_ejercicio2.md)

### **Talleres**
- **Taller 1**: Trabajando con Pods  
  Creación y gestión de Pods en Kubernetes para el despliegue de aplicaciones.
  - [Ver enunciado taller 1](./Unidad-8:Kubernetes/enunciados/e_taller1.md)
  - [Ver taller 1 resuelto](./Unidad-8:Kubernetes/enunciados_resueltos/s_taller1.txt)

- **Taller 2**: Trabajando con ReplicaSet  
  Gestión de ReplicaSets para asegurar la disponibilidad y escalabilidad de las aplicaciones.
  - [Ver eunciado taller 2](./Unidad-8:Kubernetes/enunciados/e_taller2.md)
  - [Ver taller 2 resuelto](./Unidad-8:Kubernetes/enunciados_resueltos/s_taller2.txt)

- **Taller 3**: Trabajando con Deployments  
  Despliegue y gestión de aplicaciones con Kubernetes utilizando Deployments.
  - [Ver enunciado taller 3](./Unidad-8:Kubernetes/enunciados/e_taller3.md)
  - [Ver taller 3 resuelto](./Unidad-8:Kubernetes/enunciados_resueltos/s_taller3.txt)

- **Taller 4**: Trabajando con Services  
  Exposición de aplicaciones y servicios a través de Kubernetes Services para facilitar la comunicación.
  - [Ver enunciado taller 4](./Unidad-8:Kubernetes/enunciados/e_taller4.md)

- **Taller 5**: Despliegues parametrizados  
  Implementación de despliegues parametrizados para mayor flexibilidad en la configuración de aplicaciones.

