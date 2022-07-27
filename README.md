# Devops-Netology

## Игнорирование файлов в каталоге terraform:
- Локальные каталоги .terraform
- Файлы с раширением .tfstate и файлы где встречается .tfstate. 
- Файл crash.log и файлы crash.*.log (где * любое количество символов)
- Файлы с раширением .tvars и .tfvars.json
- Файлы override.tf и override.tf.json
- Файлы в имя которых заканчивается на _override.tf и _override.tf.json
- Файлы .terraformrc, terraform.rc

## 2.4. Инструменты Git

2.4. Инструменты Git

1. **git show aefea**
- хеш: aefead2207ef7e2aa5dc81a34aedf0cad4c32545
- Коментарий: Update CHANGELOG.md

2. **git show -s 85024d3**
- Тег: tag: v0.12.23

3. **git show --pretty=%P b8d720**
- 56cd7859e05c36c06b56d013b55a252d0bb7e158
- 9ea88f22fc6269854151c571162c5bcf958bee2b

4. **git log v0.12.23..v0.12.24 --oneline**

- 33ff1c03bb (tag: v0.12.24) v0.12.24
- b14b74c493 [Website] vmc provider links
- 3f235065b9 Update CHANGELOG.md
- 6ae64e247b registry: Fix panic when server is unreachable
- 5c619ca1ba website: Remove links to the getting started guide's old location
- 06275647e2 Update CHANGELOG.md
- d5f9411f51 command: Fix bug when using terraform login on Windows
- 4b6d06cc5d Update CHANGELOG.md
- dd01a35078 Update CHANGELOG.md
- 225466bc3e Cleanup after v0.12.23 release

5. **git log -S"func providerSource(" --oneline**
- 8c928e8358 main: Consult local directories as potential mirrors of providers

6. **git grep "func globalPluginDirs("** и **git log -s -L :globalPluginDirs:plugins.go --oneline**
- 78b1220558 Remove config.go and update things using its aliases
- 52dbf94834 keep .terraform.d/plugins for discovery
- 41ab0aef7a Add missing OS_ARCH dir to global plugin paths
- 66ebff90cd move some more plugin search path logic to command
- 8364383c35 Push plugin discovery down into command package

7. **git log -S"func synchronizedWriters(" --oneline --pretty=format:"%h - %an, %s"**
- 5ac311e2a9 - Martin Atkins, main: synchronize writes to VT100-faker on Windows
- Author: Martin Atkins <mart@degeneration.co.uk>

