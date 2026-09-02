Laboratorio Semana 1: Administración de Windows y Configuración del Controlador de Dominio (Domain Controller)
Este laboratorio marca el inicio de mi preparación práctica para el soporte técnico de TI. En esta primera semana, me enfoqué en comprender los fundamentos del sistema operativo Windows a nivel empresarial, la diferencia entre identidades locales y de dominio, y la instalación de un Controlador de Dominio (Domain Controller) en un entorno de pruebas virtualizado.

Objetivos de la Semana 1
Entender la administración avanzada de Windows: Comprender la estructura de cuentas, privilegios de administrador local y la gestión de servicios del sistema.
Diferenciar Cuentas Locales vs. Cuentas de Dominio: Asimilar cómo se almacena la identidad en una base de datos local (SAM) frente a una base de datos centralizada (Active Directory).
Instalar y configurar un Controlador de Dominio (Domain Controller): Configurar Windows Server 2022 y promoverlo a Controlador de Dominio para establecer las bases de una red empresarial simulada.
Aplicar el método "Break and Fix": Identificar, documentar y resolver fallos comunes durante el despliegue del servidor.

Herramientas y Tecnologías Utilizadas
Hipervisor: Oracle VM VirtualBox (v7.0)
Sistema Operativo Servidor: Windows Server 2022 Standard (Evaluación)
Red del Laboratorio: Red Interna (Internal Network) / NAT para descargas iniciales
Consolas de Gestión: Server Manager, PowerShell, Services.msc, Local Users and Groups (lusrmgr.msc)

Guía de Implementación Paso a Paso
Paso 1: Configuración del Hipervisor e Instalación de Windows Server
Creé una máquina virtual en VirtualBox con 4 GB de RAM y 50 GB de disco virtual (VDI Dynamically Allocated).
Monté la ISO de Windows Server 2022 e inicié el instalador.
Seleccioné la versión Windows Server 2022 Standard Evaluation (Desktop Experience) para contar con la interfaz gráfica de usuario.
[Opcional: Inserta aquí una captura de pantalla de la máquina virtual ejecutando Windows Server recién instalado]
Paso 2: Configuración Inicial de Windows (Administración de Endpoints)
Antes de instalar cualquier rol de servidor, realicé tareas esenciales de administración de Windows:

Cambio de nombre del servidor: Renombré el equipo a DC-SRV01 (un nombre estandarizado y fácil de identificar).
Asignación de IP Estática: Un Controlador de Dominio requiere una dirección IP fija. Configuré la IP 192.168.10.10 con máscara de subred 255.255.255.0. El servidor DNS preferido lo apunté a sí mismo (127.0.0.1 o 192.168.10.10).
Exploración de Servicios de Windows (services.msc): Analicé los estados de los servicios (Automático, Manual, Deshabilitado) y aprendí a reiniciar de forma manual servicios clave de Windows que suelen fallar en producción.
Paso 3: Instalación de Active Directory Domain Services (AD DS)
Abrí el Server Manager y seleccioné Add Roles and Features.
Elegí Active Directory Domain Services y acepté las características requeridas adicionales.
Una vez finalizada la instalación del rol, procedí a promover el servidor a Controlador de Dominio:
Seleccioné Add a new forest (Añadir un nuevo bosque).
Definí el nombre del dominio raíz como miempresa.local (un dominio ficticio para pruebas).
Configuré la contraseña de restauración de servicios de directorio (DSRM) y finalicé el asistente. El servidor se reinició automáticamente.
[Opcional: Inserta aquí una captura de pantalla del Server Manager mostrando el rol AD DS en verde]

Sección "Break and Fix" (Romper y Reparar)
Durante la instalación y configuración inicial, me enfrenté al siguiente inconveniente práctico:

El Problema: Error de prerrequisitos de DNS durante la promoción del DC
Síntoma: Al intentar promover el servidor a Controlador de Dominio, el asistente mostraba una advertencia crítica indicando que no se podía crear una delegación para el servidor DNS o que la prueba de prerrequisitos fallaba por conectividad IP errónea.
Metodología de Troubleshooting aplicada:
¿Alguna vez funcionó?: No, es una instalación limpia de laboratorio.
¿Qué cambió?: Activé el rol de AD DS y cambié el adaptador de red en VirtualBox a "Red Interna".
¿A quién más afecta?: De momento solo a este servidor aislado.
Diagnóstico técnico: Al revisar la configuración de red con ipconfig /all en la consola de comandos, noté que la tarjeta de red del servidor estaba configurada para obtener direccionamiento dinámico y había tomado una dirección autoconfigurada APIPA (169.254.x.x) debido a que no existía un servidor DHCP activo en la "Red Interna" de VirtualBox.
Resolución:
Fui a las propiedades de red del adaptador IPv4 de Windows Server.
Definí manualmente la dirección IP estática (192.168.10.10) y la puerta de enlace predeterminada.
Coloqué el DNS preferido apuntando al propio servidor para que el instalador de Active Directory configurara correctamente el rol DNS integrado.
Ejecuté nuevamente el análisis de prerrequisitos del asistente y la verificación pasó de manera exitosa.

Conceptos Clave Aprendidos esta Semana
Cuenta Local vs. Cuenta de Dominio: Las cuentas locales pertenecen únicamente a la máquina donde fueron creadas y se almacenan en la base de datos SAM local. Las cuentas de dominio residen de forma segura y centralizada en Active Directory y permiten iniciar sesión en cualquier endpoint de la red que esté unido al dominio.
Privilegios de Administrador Local: Entendí el peligro de que los usuarios finales tengan permisos de administrador local en producción (pueden deshabilitar antivirus, instalar software malicioso, etc.). En soporte de Help Desk, el principio de menor privilegio es fundamental.
Active Directory: Es la base de datos de identidades y recursos que permite la administración centralizada de políticas de seguridad, usuarios y endpoints dentro de una infraestructura empresarial.
