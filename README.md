# Guía Completa: Configurar Múltiples Cuentas GitHub con SSH (Equipo y Personal)

Esta guía te permitirá:
Crear y configurar claves SSH para ambas cuentas.
Conectar repositorios locales al remoto correcto según la cuenta.
Gestionar autenticación sin conflictos entre proyectos personales y del equipo.



### 🔑 Paso 1: Generar Claves SSH para Cada Cuenta

1.1 Generar clave SSH para cuenta del equipo
Abre Git Bash y ejecuta:

```En git bash
ssh-keygen -t ed25519 -C "equipo@empresa.com" -f ~/.ssh/id_ed25519_equipo
Presiona Enter para guardar en la ubicación default.
```
Agrega una passphrase (recomendado para seguridad).

1.2 Generar clave SSH para cuenta personal

```En git bash
ssh-keygen -t ed25519 -C "personal@email.com" -f ~/.ssh/id_ed25519_personal
```
Sigue los mismos pasos que antes.

### ⚙️ Paso 2: Configurar el Archivo ~/.ssh/config


Crea o edita el archivo de configuración SSH:

```En git bash
nano ~/.ssh/config
```
Guarda (Ctrl + O, luego Enter)

Luego entra a Windows: C:\Users\[tu_usuario]\.ssh\config, y abre el archivo “config”,En Windows, puedes usar Notepad++ o VS Code, luego pega la siguiente configuración.

```
# Cuenta del equipo
Host github.com-equipo
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_equipo

# Cuenta personal
Host github.com-personal
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_personal
```

### 🔑 Paso 3: Dónde Agregar las Claves SSH en GitHub

##### 📌 Pasos para agregar la clave SSH a tu cuenta de GitHub:
Inicia sesión en GitHub con la cuenta correspondiente (EQUIPO o PERSONAL).

Ve a Configuración:
Haz clic en tu foto de perfil (esquina superior derecha) → Settings.

Navega a "SSH and GPG Keys":
En el menú lateral izquierdo, selecciona SSH and GPG Keys.

Agrega una nueva clave SSH:
Haz clic en New SSH Key o Add SSH Key.

Completa los detalles:

Title: Un nombre descriptivo (ej: Laptop Personal o PC Trabajo).
Key Type: Deja Authentication Key.
Key: Pega aquí tu clave pública (la que generaste con ```cat ~/.ssh/id_ed25519_equipo.pub```  o ```cat ~/.ssh/id_ed25519_personal.pub```).

Haz clic en "Add SSH Key".

🔍 ¿Cómo copiar la clave pública correctamente?
Ejecuta en la terminal:

```En git bash

# Para Equipo:
cat ~/.ssh/id_ed25519_equipo.pub

# Para Personal:
cat ~/.ssh/id_ed25519_personal.pub
```

### ✅ Paso 4: Verificar que las Claves Estén Configuradas Correctamente

Ejecuta en la terminal:

```En git bash

# Probar conexión para Equipo:
ssh -T git@github.com-equipo
# Debe mostrar: "Hi Equipo! You've successfully authenticated..."

# Probar conexión para Personal:
ssh -T git@github.com-personal
# Debe mostrar: "Hi Personal! You've successfully authenticated..."

```

### 📥 Paso 5: Clonar el Repositorio Remoto con SSH (usando Equipo)

Abre la carpeta donde trabajaras con tu proyecto (Repositorio remoto) 

En la terminal de VS ejecuta:
```
git init
```
Despues


Asegúrate de que tu ~/.ssh/config está configurado correctamente (como en el Paso 2):
```
## La URL original es:
git@github.com:ASI0729-Final-Project/docs.git

## La URL modificada (para usar la clave SSH de Equipo) es:
git@github.com-equipo:ASI0000-Final-Project/docs.git
```
Clona el repositorio con el nuevo host:

```En git bash
git clone git@github.com-equipo:ASI0729-Final-Project/docs.git
```
Esto asegura que Git usará la clave SSH correcta (id_ed25519_equipo).


### ⚙️ Paso 6: Configurar Git Local para el Repositorio Clonado

``` En git bash
cd docs
```

Configura el nombre y correo localmente (solo para este repositorio):

``` En git bash
git config user.name "Equipo"
git config user.email "tu_correo_asociado@github.com"  # Usa el correo de tu cuenta Equipo
```

Verifica la configuración:
``` En git bash
git config --local user.name  # Debe devolver "Equipo"
git config --local user.email # Debe devolver tu correo de GitHub
```

### ✅ Paso 7: Verificar que Todo Funcione Correctamente

Verifica que estás autenticado como Equipo:

```En git bash
ssh -T git@github.com-equipo
```

##### Salida esperada:
Hi Equipo! You've successfully authenticated...

### Paso 8: Crea una rama para cada Issue 🛠
Formato del nombre: [tipo]/[issue-number]-[descripción-breve]
Ejemplo: feature/123-add-login-form o fix/456-resolve-responsive-bug
```
git checkout -b feature/123-nombre-del-issue
```
🔄 Paso Crítico Antes del PR: Sincronizar con Main 🔄
¡Evita conflictos al mergear! 🚨

##### Sincronizar tu Rama con Main 📥
Sigue este flujo ANTES de crear el PR:

Cambia a la rama principal (main/master):
```
git checkout main
```
Descarga los últimos cambios:
```
git pull origin main
```
Vuelve a tu rama de trabajo:
```
git checkout feat/45-nombre-del-issue
```
Mergea main en tu rama:
```
git merge main
```
⚠️ Si hay conflictos:
Resuélvelos manualmente en tu editor de código.
Usa ```git status``` para identificar archivos en conflicto.
Haz commit de la sincronización:
```
git commit -m "chore: merge main into feat/45-nombre-del-issue"
```
Sube los cambios actualizados:
```
git push  

##Si no funciona VS te dira que uses algo asi, este si funciona: 
git push --set-upstream origin feat/45-nombre-del-issue

```

##### Crear el Pull Request (PR) 🎯
Dirígete a GitHub y haz clic en "Compare & pull request". (Puedes hacerlo con el link que te proporcióna la terminal de VS)
Título del PR: Usa el mismo formato que el issue (ej: Feat: Login Form #123).
Descripción:
Explica los cambios realizados.
Vincula el issue con ```Resolves #123``` o ```Closes #123```.
Reviewers: Asigna a quien deba revisar el código.
Merge: Una vez aprobado, fusiona con la rama principal (main/master/develop).

