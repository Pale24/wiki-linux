# 1 - Linux y sus distribuciones
## Introducción 

Cuando decimos "Linux", en general nos referimos al sistema operativo (SO) completo pero estrictamente hablando Linux solamente es una parte del SO (el kernel), para referirnos correctamente deberiamos decir GNU/Linux. El kernel o núcleo del sistema operativo es una pieza de software fundamental que se encarga de:

- Administrar el uso de los recursos del equipo.
- Facilitar la interacción entre las aplicaciones y el hardware.

Linux es uno de los sistemas operativos más populares; su desarrollo se inició en 1991 por Linus Torvalds y se inspiró en Unix, otro sistema operativo desarrollado en la década de 1970 por AT&T Laboratories. Unix estaba orientado a pequeñas computadoras; en ese momento, las computadoras “pequeñas” se consideraban máquinas que no necesitaban una sala completa con aire acondicionado y costaban menos de un millón de dólares. Más tarde, fueron consideradas como máquinas que podían ser transportadas por dos personas. En ese momento, Unix no estaba disponible en computadoras pequeñas como las computadoras de oficina basadas en la plataforma x86. Por lo tanto, Linus, que era un estudiante en ese momento, comenzó a implementar un sistema operativo tipo Unix que se suponía que debía ejecutarse en esta plataforma.

Principalmente, Linux usa los mismos principios e ideas básicas de Unix, pero Linux en sí no contiene código Unix, ya que es un proyecto independiente. Linux no está respaldado por una compañía individual sino por una comunidad internacional de programadores. Como está disponible gratuitamente, puede ser utilizado por cualquier persona sin restricciones.

### GNU

A mediados de la década de 1980, Richard Stallman (ex-científico del Massachusetts Institute of Technology (MIT) fundó la Free Software Foundation y comenzó el Proyecto GNU (GNU's Not Unix) con la intención de crear un sistema operativo que respetara la libertad de los usuarios de usarlo, estudiarlo, modificarlo, y distribuirlo sin restricciones. Este proyecto aportó en su momento (y continúa aportando) software para las distribuciones Linux que usamos hoy en día.

De esta iniciativa también surgieron lo que hoy conocemos como las 4 libertades fundamentales del software libre:

- La libertad de ejecutar el programa como se desee, con cualquier propósito (libertad 0).
- La libertad de estudiar cómo funciona el programa, y cambiarlo para que haga lo que uno quiera (libertad 1).
- La libertad de redistribuir copias para ayudar otros (libertad 2).
- La libertad de distribuir copias de las versiones modificadas a terceros (libertad 3). 

Los esfuerzos de Linus Torvalds se vieron complementados por la iniciativa del proyecto GNU. Es decir, lo que uno tenía le faltaba al otro, y al unir el kernel desarrollado por Linus con todas las herramientas desarrolladas por el proyecto GNU se obtuvo GNU/Linux.

### Distribuciones

Una distribución de Linux es un paquete que consiste en un kernel de Linux y una selección de aplicaciones mantenidas por una empresa o comunidad de usuarios. El objetivo de una distribución es optimizar el núcleo y las aplicaciones que se ejecutan en el sistema operativo para un determinado caso de uso o grupo de usuarios. Las distribuciones a menudo incluyen herramientas específicas de distribución para la instalación de software y la administración del sistema. Es por esto que algunas distribuciones se usan principalmente para entornos de escritorio los cuales deben ser fáciles de usar, mientras que otras se usan principalmente para ejecutarse en servidores utilizando los recursos disponibles de la manera más eficiente posible.

Otra forma de clasificar las distribuciones es haciendo referencia a la distribución familiar a la que pertenecen. Las distribuciones de la familia de **Debian** utilizan el gestor de paquetes *dpkg* para gestionar el software que se ejecuta en el sistema operativo. Los paquetes que pueden instalarse con su gestor son mantenidos por miembros voluntarios de la comunidad y utilizan el formato de paquete *deb* para especificar cómo se instala el software en el sistema operativo y cómo está configurado de forma predeterminada. Al igual que una distribución, un paquete es un conjunto de software con su correspondiente configuración y documentación que facilita el proceso de instalación, actualización y uso del software.

La distribución ***Debian GNU/Linux*** es la distribución más grande de la familia Debian. El proyecto Debian GNU/Linux fue lanzado por Ian Murdock en 1993 y hoy en día miles de voluntarios están trabajando en el proyecto con el objetivo de proporcionar un sistema operativo muy fiable y promover la visión de Richard Stallman de un sistema operativo que respete las libertades del usuario para ejecutar, estudiar, distribuir y mejorar el software. Esta es la razón por la cual no proporciona ningún software propietario por defecto.

***Ubuntu*** es otra distribución basada en Debian que vale la pena mencionar. Ubuntu fue creado por Mark Shuttleworth y su equipo en 2004, con la misión de brindar un entorno de escritorio fácil de usar. La misión de Ubuntu es proporcionar software gratuito a todos en todo el mundo, así como reducir el costo de los servicios profesionales. La distribución tiene lanzamientos programados cada seis meses con un soporte a largo plazo cada 2 años.

***Red Hat*** es una distribución de Linux desarrollada y mantenida por la compañía de software con el mismo nombre, que fue adquirida por IBM en 2019. La distribución de Red Hat Linux se inició en 1994 y se renombró en 2003 a Red Hat Enterprise Linux, a menudo abreviado como RHEL. Se proporciona a las empresas como una solución empresarial confiable que es compatible con Red Hat e incluye software que tiene como objetivo facilitar el uso de Linux en entornos de servidores profesionales. Algunos de sus componentes requieren suscripciones o licencias de pago. El proyecto ***CentOS*** (actualmente discontinuado) utilizaba el código fuente disponible de Red Hat Enterprise Linux y lo compilaba en una distribución totalmente gratuita. 

Tanto RHEL como CentOS están optimizados para su uso en entornos de servidor. El proyecto Fedora se fundó en 2003 y crea una distribución de Linux dirigida a computadoras de escritorio. Red Hat inició y mantiene la distribución de Fedora desde entonces. Fedora es muy progresista y adopta nuevas tecnologías muy rápidamente y a veces se considera un banco de pruebas para nuevas tecnologías que luego podrían incluirse en RHEL. Todas las distribuciones basadas en Red Hat usan el formato de paquete rpm.

La empresa SUSE fue fundada en 1992 en Alemania como un proveedor de servicios Unix. La primera versión de SUSE Linux se lanzó en 1994. A lo largo de los años, SUSE Linux se hizo conocido principalmente por su herramienta de configuración YaST. Esta herramienta permite a los administradores instalar y configurar software y hardware, así como servicios y redes. Al igual que RHEL, SUSE lanza ***SUSE Linux Enterprise Server***, que es su edición comercial. Esta distribución se publica con menos frecuencia y es adecuada para la implementación empresarial y de producción. Se distribuye como un servidor, así como un entorno de escritorio con paquetes adecuados para el propósito. En 2004, SUSE lanzó el proyecto ***openSUSE***, que abrió oportunidades para que los desarrolladores y usuarios probaran y desarrollaran aún más el sistema. La distribución de openSUSE está disponible gratuitamente para su descarga.

A lo largo de los años se han lanzado distribuciones independientes, algunas basadas en Red Hat o Ubuntu, otras diseñadas para mejorar una propiedad específica de un sistema o hardware. Otras construidas con funcionalidades específicas como ***QubesOS***, un entorno de escritorio muy seguro, o ***Kali Linux***, que proporciona un entorno para explotar las vulnerabilidades de software, utilizado principalmente por los expertos en pruebas de penetración, y otras superpequeñas distribuciones de Linux diseñadas para ejecutarse de forma específica en contenedores Linux, como Docker. También hay distribuciones creadas específicamente para componentes de sistemas embebidos e incluso dispositivos inteligentes.

[Listado de distribuciones linux](https://distrotest.net/index.php)

### Sistemas embebidos

Los sistemas embebidos son una combinación de hardware y software diseñados para tener una función específica dentro de un gran sistema. Por lo general, forman parte de otros dispositivos que ayudan a controlarlos. Los sistemas embebidos se encuentran en aplicaciones de automoción, médicas e incluso militares. Debido a su gran variedad de aplicaciones varios sistemas operativos basados en el kernel de Linux han sido desarrollados para ser utilizados en sistemas embebidos. Una parte importante de los dispositivos inteligentes tiene un sistema operativo basado en el kernel de Linux.

Por lo tanto, con los sistemas embebidos surge el software embebido. El propósito de este software es acceder al hardware y hacerlo utilizable. Entre las principales ventajas de Linux sobre cualquier software embebido propietario se encuentran, la compatibilidad de plataformas entre vendedores, el desarrollo, el soporte y la ausencia de cuotas por concepto de licencia. Dos de los proyectos de software embebido más populares son Android, que se utiliza principalmente en teléfonos móviles a través de una variedad de proveedores, y Raspbian que se utiliza principalmente en Raspberry Pi.

#### Android

Android es un sistema operativo para dispositivos móviles desarrollado principalmente por Google. Android Inc. fue fundado en 2003 en Palo Alto, California. La compañía inicialmente creó un sistema operativo destinado a funcionar en cámaras digitales. En 2005, Google adquirió Android Inc. y lo desarrolló para convertirse en uno de los mayores sistemas operativos para dispositivos móviles.

La base de Android es una versión modificada del kernel de Linux con software adicional de código abierto. El sistema operativo está desarrollado principalmente para dispositivos con pantalla táctil, pero Google ha desarrollado versiones para televisores y relojes de pulsera. Se han desarrollado diferentes versiones de Android para consolas de juegos, cámaras digitales y PCs.

El código abierto de Android está disponible gratuitamente como Android Open Source Project (AOSP). Google ofrece una serie de componentes propietarios además del núcleo de Android. Entre los componentes se incluyen aplicaciones como Google Calendar, Google Maps, Google Mail, el navegador Chrome y Google Play Store, que facilita la instalación de aplicaciones. La mayoría de los usuarios consideran estas herramientas una parte integral de su experiencia con Android. Por lo tanto, casi todos los dispositivos móviles enviados con Android a Europa y América incluyen el software patentado de Google.

Android en dispositivos integrados tiene muchas ventajas. El sistema operativo es intuitivo y fácil de usar con una interfaz gráfica de usuario, tiene una comunidad de desarrolladores muy amplia, así que es fácil encontrar ayuda para el desarrollo. También es compatible con la mayoría de los proveedores de hardware con un controlador de Android, por lo tanto, es fácil y rentable crear un prototipo para un sistema entero.

#### Raspbian y Raspberry Pi

Raspberry Pi es una computadora de bajo costo y del tamaño de una tarjeta de crédito que puede funcionar como un ordenador de sobremesa con todas sus funciones, pero que a su vez puede utilizarse dentro de un sistema Linux integrado. Este ordenador fue desarrollado por la Fundación Raspberry Pi, que es una organización benéfica educativa con sede en el Reino Unido. Su principal propósito es enseñar a los jóvenes a aprender a programar y comprender la funcionalidad de las computadoras. El Raspberry Pi se puede diseñar y programar para realizar tareas u operaciones que forman parte de un sistema mucho más complejo.

Las especialidades de Raspberry Pi incluyen un conjunto de pines de Entrada/Salida de Propósito General (GPIO) que pueden ser utilizados para acoplar dispositivos electrónicos y placas de extensión, lo que permite utilizar Raspberry Pi como plataforma para el desarrollo de hardware, ya que a pesar de que estaba pensado para fines educativos se utiliza hoy en día en varios proyectos de DIY (hágalo usted mismo), así como para la creación de prototipos industriales en el desarrollo de sistemas embebidos.

La Raspberry Pi utiliza procesadores ARM. Varios sistemas operativos, incluido Linux, corren sobre Raspberry Pi. Como Raspberry Pi no contiene un disco duro, el sistema operativo se inicia desde una tarjeta de memoria SD. Una de las distribuciones de Linux más destacadas para Raspberry Pi es Raspbian. Como su nombre indica, pertenece a la familia de distribución de Debian. Está personalizado para instalarse en el hardware de Raspberry Pi y proporciona más de 35000 paquetes optimizados para este entorno. Además de Raspbian, existen muchas otras distribuciones de Linux para Raspberry Pi, como, por ejemplo, Kodi, que convierte a Raspberry Pi en un centro multimedia.

### Linux y el Cloud Computing

El término cloud computing se refiere a una forma estandarizada de consumir recursos informáticos, ya sea comprándolos a un proveedor público de cloud computing o ejecutando una nube privada. Según informes del 2017, Linux ejecuta el 90% de la carga de trabajo de la nube pública. Todos los proveedores de cloud computing, desde Amazon Web Services (AWS) hasta Google Cloud Platform (GCP) ofrecen diferentes formas de trabajar con Linux. Incluso Microsoft, una empresa cuyo antiguo CEO comparó Linux con un cáncer, ofrece hoy en día máquinas virtuales basadas en Linux en su nube Azure.

Linux generalmente es ofrecido como parte de Infrastructure as a Service (IaaS). Las instancias IaaS son máquinas virtuales que se aprovisionan en cuestión de minutos en la nube. Cuando se inicia una instancia IaaS, se elige una imagen que contiene los datos que se desplegarán en la nueva instancia. Los proveedores de nube ofrecen varias imágenes que contienen instalaciones listas para ejecutar tanto de las distribuciones populares, así como sus propias versiones de Linux. El usuario podrá elegir una imagen que contiene su distribución preferida y acceder a una instancia de la nube que ejecute esta distribución poco después de haberse creado. La mayoría de los proveedores agregan herramientas a sus imágenes para ajustar la instalación a una instancia específica de la nube. Estas herramientas pueden, por ejemplo, extender los sistemas de archivos de la imagen para que se ajusten al disco duro real de la máquina virtual.

[Top 500](https://en.wikipedia.org/wiki/TOP500)

- Fuentes:
	- [Linux essentials](https://learning.lpi.org/es/learning-materials/010-160/1/1.1/1.1_01/)
