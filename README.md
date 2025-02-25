# **Servicios de Red e Internet + HLC**

## **Unidad 6: Protocolo DNS**

### **Teoría**
- **Resolución de nombres de dominios en sistemas Linux**  
  Estudio de cómo Linux resuelve los nombres de dominio y el papel del archivo `/etc/hosts` en el proceso.
  
- **Protocolo DNS**  
  Explicación detallada del protocolo DNS, su arquitectura y funcionamiento dentro de la infraestructura de Internet.

### **Ejercicios**
- **Ejercicio 1**: Resolución de nombres de dominios en sistemas Linux  
  Practicar la resolución de nombres utilizando herramientas nativas de Linux. [Ver ejercicio 1](Unidad-6:Protocolo-DNS/enunciados/e_ejercicio1.md)
  
- **Ejercicio 2**: Consultas DNS con `dig`  
  Uso de la herramienta `dig` para realizar consultas DNS detalladas.

### **Servidor DNS bind9**
- **Taller 1**: Instalación y configuración del servidor bind9 en nuestra red local  
  Instrucciones paso a paso para configurar un servidor DNS usando `bind9`.
  
- **Taller 2**: Instalación y configuración de un servidor DNS esclavo  
  Explicación de la configuración de un servidor DNS esclavo para replicar la información del servidor primario.

- **Taller 3**: Delegación de subdominios con bind9  
  Establecer subdominios y delegar su gestión a servidores distintos.

- **Taller 4**: Implementación de un DNS dinámico  
  Configuración de DNS dinámico para cambiar automáticamente las direcciones IP asociadas a un nombre de dominio.

### **Prácticas**
- **Práctica (1 / 2)**: Servidores Web, Base de Datos y DNS en nuestro escenario de OpenStack  
  Desplegar servidores de DNS junto con servidores web y bases de datos en un entorno de OpenStack.

- **Práctica (2 / 2)**: Servidores Web, Base de Datos y DNS en nuestro escenario de OpenStack  
  Continuación de la práctica anterior con ajustes y configuraciones avanzadas.

---

## **Unidad 7: Servidor de Correo Electrónico**

### **Teoría**
- **Curso Correo Electrónico**  
  Estudio del funcionamiento y la arquitectura de los servidores de correo electrónico, los protocolos SMTP, IMAP y POP3.

- **Resumen: Servidor de correos**  
  Revisión de la teoría sobre la implementación y administración de servidores de correo electrónico.

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
- **Curso Kubernetes**  
  Introducción al uso de Kubernetes para la gestión de contenedores y despliegue de aplicaciones en un entorno de producción.

### **Ejercicios**
- **Ejercicio 1**: Instalación y configuración de minikube y kubectl  
  Paso a paso para instalar y configurar Minikube y Kubectl para gestionar clusters de Kubernetes locales.

### **Talleres**
- **Taller 1**: Trabajando con Pods  
  Creación y gestión de Pods en Kubernetes para el despliegue de aplicaciones.

- **Ejercicio 2**: Trabajando con un Pod multicontenedor (VOLUNTARIO)  
  Implementación de un pod que contenga múltiples contenedores, permitiendo la comunicación entre ellos.

- **Taller 2**: Trabajando con ReplicaSet  
  Gestión de ReplicaSets para asegurar la disponibilidad y escalabilidad de las aplicaciones.

- **Taller 3**: Trabajando con Deployments  
  Despliegue y gestión de aplicaciones con Kubernetes utilizando Deployments.

- **Taller 4**: Trabajando con Services  
  Exposición de aplicaciones y servicios a través de Kubernetes Services para facilitar la comunicación.

- **Taller 5**: Despliegues parametrizados  
  Implementación de despliegues parametrizados para mayor flexibilidad en la configuración de aplicaciones.

