# El Capulin

python script recursivo con seleccion de rutas mediante sistemas con un enfoque declarativo

"El Capulin" es un proyecto de scripting en Python diseñado para la **detección automatizada y confiable del sistema operativo y sus variantes**, permitiendo una **selección dinámica de rutas y configuraciones específicas**. Su metodología se centra en un enfoque **declarativo**, que separa la lógica de detección de los datos de configuración, y en una **arquitectura modular** que facilita la extensibilidad y el mantenimiento. Este documento detalla los principios de diseño, la estructura de archivos propuesta y cómo la información se declara y utiliza en el script.

## Soporte de Sistemas Operativos y Distribuciones

"El Capulin" estará diseñado para detectar y manejar los siguientes entornos:

- **Sistemas Operativos Principales:**
  - **Windows 11:** Con enfoque en la detección de su versión específica y potencial uso de WSL.
  - **Kali Linux:** Identificación precisa de la distribución para ofrecer herramientas y rutas específicas de seguridad.
  - **Arch Linux:** Reconocimiento de su naturaleza *rolling release* y rutas comunes.
  - **NixOS:** Detección de su filosofía declarativa y sugerencia de rutas de configuración clave (ej., `/etc/nixos/configuration.nix`).
  - **macOS Sequoia:** Detección de la versión más reciente para compatibilidad.
  - **Linux Genérico:** Un *fallback* para cualquier otra distribución Linux no reconocida específicamente, ofreciendo información general sobre Nix y NixOS.
- **Identificación de distribuciones Linux específicas:** Se priorizará la detección de NixOS, Arch, Debian y Ubuntu antes de clasificar un sistema como "Linux genérico".

## Etructura de Carpetas y Archivos del Proyecto

La organización del proyecto será fundamental para su mantenibilidad y extensibilidad, reflejando una jerarquía lógica de sistemas operativos y sus componentes.

```
.
├── elcapulin.py              # Main script for OS detection and execution
├── config/                    # Configuration directory for better organization
│   ├── base.yaml             # Base configuration shared across all OS
│   ├── os_specific/          # OS-specific configurations
│   │   ├── windows.yaml
│   │   ├── linux.yaml
│   │   └── macos.yaml
│   └── messages/             # Localized message templates
│       ├── en/
│       └── es/
├── utils/                    # Utility functions module
│   ├── __init__.py
│   ├── file_handlers.py      # File I/O operations
│   ├── command_runner.py     # Shell command execution
│   ├── logger.py             # Logging utilities
│   └── validators.py         # Input validation functions
├── os_detect/                # OS detection module
│   ├── __init__.py
│   ├── base.py              # Base detection class
│   ├── linux.py             # Linux detection
│   ├── windows.py           # Windows detection
│   └── macos.py             # macOS detection
├── data/                     # OS-specific data and templates
│   ├── Linux/
│   │   ├── Distros/
│   │   │   ├── Debian/
│   │   │   │   ├── scripts/
│   │   │   │   └── configs/
│   │   │   ├── Kali/
│   │   │   │   ├── security_tools/
│   │   │   │   └── pentesting/
│   │   │   └── NixOS/
│   │   │       ├── configurations/
│   │   │       └── modules/
│   │   ├── Tools/
│   │   │   ├── Bootloaders/
│   │   │   │   ├── grub/
│   │   │   │   └── systemd-boot/
│   │   │   └── Utilities/
│   │   │       ├── system_info/
│   │   │       └── maintenance/
│   │   └── Docs/
│   │       ├── setup_guides/
│   │       └── troubleshooting/
│   ├── Windows/
│   │   ├── Programs/
│   │   │   ├── installers/
│   │   │   └── configs/
│   │   ├── System/
│   │   │   ├── registry/
│   │   │   └── services/
│   │   └── Docs/
│   │       ├── setup/
│   │       └── wsl/
│   ├── MacOS/
│   │   ├── Programs/
│   │   │   ├── homebrew/
│   │   │   └── defaults/
│   │   └── Docs/
│   │       ├── setup/
│   │       └── migration/
│   └── common/               # Shared resources across all OS
│       ├── templates/
│       └── scripts/
├── tests/                    # Test suite directory
│   ├── __init__.py
│   ├── test_os_detect/
│   ├── test_utils/
│   └── fixtures/
├── docs/                     # Project documentation
│   ├── api/
│   ├── examples/
│   └── contributing/
├── scripts/                  # Development and maintenance scripts
│   ├── setup.py
│   └── validate_config.py
├── .gitignore
├── requirements.txt          # Python dependencies
├── pyproject.toml           # Project metadata and build settings
└── README.md                # Project documentation
```

 Estrctura de modulos del scritinig esplicados modulo por modulo

------------------

### 

### Configuración (`config/`)

La gestión de configuración se organiza jerárquicamente:

```plaintext
config/
├── base.yaml                 # Configuración base
│   ├── logging_config       # Niveles y formatos de logging
│   ├── default_paths        # Rutas de instalación predeterminadas
│   └── feature_flags        # Activación de características
├── os_specific/             # Configuraciones específicas por OS
│   ├── windows.yaml         # Configuración Windows
│   │   ├── registry_paths   # Rutas del registro
│   │   ├── wsl_config      # Configuración de WSL
│   │   └── powershell_profiles
│   ├── linux.yaml
│   │   ├── package_managers # Gestores de paquetes
│   │   ├── desktop_envs    # Entornos de escritorio
│   │   └── systemd_units   # Servicios del sistema
│   └── macos.yaml
│       ├── homebrew_taps   # Fuentes de Homebrew
│       ├── defaults_write  # Configuraciones del sistema
│       └── xcode_select    # Herramientas de desarrollo
└── messages/                # Plantillas de mensajes
    ├── en/                 # Inglés
    │   ├── errors.yaml
    │   ├── success.yaml
    │   └── prompts.yaml
    └── es/                 # Español
        └── [misma estructura que en/]
```

Explicación de la Estructura:

- **`elcapulin.py`**: El punto de entrada del script. Se encargará de cargar la configuración, llamar a la función de detección de OS y luego a la función de presentación de información o ejecución de comandos basada en el OS detectado.

-----------------

### os_detect_structure (`os_detect_structure/`)

La gestión de configuración se organiza jerárquicamente:

```plaintext
config/
├── base.yaml                 # Configuración base
│   ├── logging_config       # Niveles y formatos de logging
│   ├── default_paths        # Rutas de instalación predeterminadas
│   └── feature_flags        # Activación de características
├── os_specific/             # Configuraciones específicas por OS
│   ├── windows.yaml         # Configuración Windows
│   │   ├── registry_paths   # Rutas del registro
│   │   ├── wsl_config      # Configuración de WSL
│   │   └── powershell_profiles
│   ├── linux.yaml
│   │   ├── package_managers # Gestores de paquetes
│   │   ├── desktop_envs    # Entornos de escritorio
│   │   └── systemd_units   # Servicios del sistema
│   └── macos.yaml
│       ├── homebrew_taps   # Fuentes de Homebrew
│       ├── defaults_write  # Configuraciones del sistema
│       └── xcode_select    # Herramientas de desarrollo
└── messages/                # Plantillas de mensajes
    ├── en/                 # Inglés
    │   ├── errors.yaml
    │   ├── success.yaml
    │   └── prompts.yaml
    └── es/                 # Español
        └── [misma estructura que en/]
```

### 

----

### test_estructure (`test_structure/`)

Configuración (`config/`)

La gestión de configuración se organiza jerárquicamente:

```plaintext
config/
├── base.yaml                 # Configuración base
│   ├── logging_config       # Niveles y formatos de logging
│   ├── default_paths        # Rutas de instalación predeterminadas
│   └── feature_flags        # Activación de características
├── os_specific/             # Configuraciones específicas por OS
│   ├── windows.yaml         # Configuración Windows
│   │   ├── registry_paths   # Rutas del registro
│   │   ├── wsl_config      # Configuración de WSL
│   │   └── powershell_profiles
│   ├── linux.yaml
│   │   ├── package_managers # Gestores de paquetes
│   │   ├── desktop_envs    # Entornos de escritorio
│   │   └── systemd_units   # Servicios del sistema
│   └── macos.yaml
│       ├── homebrew_taps   # Fuentes de Homebrew
│       ├── defaults_write  # Configuraciones del sistema
│       └── xcode_select    # Herramientas de desarrollo
└── messages/                # Plantillas de mensajes
    ├── en/                 # Inglés
    │   ├── errors.yaml
    │   ├── success.yaml
    │   └── prompts.yaml
    └── es/                 # Español
        └── [misma estructura que en/]
```

### 

-------

### data_structure (`data_estcrture/`)

Configuración (`data/`)

La gestión de configuración se organiza jerárquicamente:

```plaintext
data/
├── Linux/
│ ├── Distros/
│ │ ├── Debian/
│ │ │ ├── scripts/
│ │ │ │ ├── post_install/ # Post-installation tasks
│ │ │ │ └── maintenance/ # System maintenance
│ │ │ └── configs/
│ │ │ ├── apt/ # Package management
│ │ │ └── sources/ # Repository sources
│ │ └── NixOS/
│ │ ├── configurations/
│ │ │ ├── hardware/ # Hardware-specific configs
│ │ │ └── services/ # Service definitions
│ │ └── modules/
│ │ ├── system/ # System configurations
│ │ └── user/ # User environments
│ └── Tools/
│ ├── Security/ # New security tools section
│ │ ├── audit/ # System auditing
│ │ └── hardening/ # Security hardening
│ └── Performance/ # New performance tools
 ├── monitoring/ # System monitoring
 └── optimization/ # System optimization
```
