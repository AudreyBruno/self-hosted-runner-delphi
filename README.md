### Build automático de projeto Delphi com self-hosted runner (GitHub Actions)

**Pré-requisitos**

* PC ou VM com **Windows**.
* **Delphi instalado e configurado**.
* Acesso ao repositório no **GitHub**.

---

## 1) Testar build manual com MSBuild

Antes de configurar automação, valide o build no runner.

1. Abra o **Prompt de Comando**.
2. Execute o script de ambiente do Delphi:

```
call "C:\Program Files (x86)\Embarcadero\Studio\23.0\bin\rsvars.bat"
```

> Ajuste o caminho conforme a versão do Delphi instalada (exemplo acima baseado no Delphi 12).

3. Compile o projeto:

```
msbuild nomeDoSeuProjeto.dproj /t:Build /p:Config=Release /p:Platform=Win32
```

Se o build concluir sem erros, o ambiente está pronto.

---

## 2) Criar um self-hosted runner

1. No repositório, vá em:

   ```
   Settings → Actions → Runners → New self-hosted runner
   ```
2. Escolha **Windows**.
3. Siga os comandos fornecidos pelo GitHub no PC/VM que executará os builds.
4. Inicie o runner e confirme que ele aparece como **online** na lista.

---

## 3) Criar o workflow

Você pode criar diretamente no repositório ou localmente.

**Caminho do arquivo:**

```
.github/workflows/delphi-build.yml
```

**Exemplo de workflow:**

```yaml
name: Build Delphi

on:
  push:
    branches: [ main ]
  workflow_dispatch:

permissions:
  contents: write

jobs:
  build:
    runs-on: self-hosted

    steps:
      - name: Checkout
        uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Build Win32
        shell: cmd
        run: |
          call "C:\Program Files (x86)\Embarcadero\Studio\23.0\bin\rsvars.bat"
          msbuild nomeDoProjeto.dproj /t:Build /p:Config=Release /p:Platform=Win32
```
[Ver config completa](./delphi-build.yml)

---

### 4) Execução do build e publicação nas Releases

Após fazer **push** no repositório:

1. Acesse a aba **Actions** do repositório no **GitHub**.
2. O workflow será executado automaticamente no self-hosted runner configurado.
3. Ao finalizar o build, o próprio workflow publica os binários gerados (EXE/APK) diretamente em **Releases** do repositório.
4. Para baixar os arquivos, acesse:

   ```
   Repositório → Releases → última release criada pelo workflow
   ```
