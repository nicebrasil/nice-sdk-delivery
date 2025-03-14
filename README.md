# 📌 Roteiro para Instalar a Nice.SDK.Api no IIS do Windows

## 1️⃣ Instalar os Pré-requisitos

### 1.1 Instalar o IIS

Se o IIS ainda não estiver instalado, execute no PowerShell como administrador:

```
Enable-WindowsOptionalFeature -Online -FeatureName IIS-WebServer -All
```

Ou instale manualmente via Painel de Controle → Ativar ou desativar recursos do Windows → Marcar "Servidor Web (IIS)".

### 1.2 Instalar o .NET 8 Hosting Bundle
Baixe e instale o .NET 8 Hosting Bundle:

🔗 [Download do Hosting Bundle .NET 8](https://dotnet.microsoft.com/en-us/download/dotnet/8.0)

---

### 2️⃣ Instalar o MySQL no Windows
A API usa MySQL, portanto é necessário que seja instalado e configurado o banco de dados no servidor.

### 2.1 Baixar e Instalar o MySQL

1. Acesse: 🔗[Download do MySQL](https://dev.mysql.com/downloads/installer/).

2. Baixe o MySQL Installer for Windows.

3. Na instalação, escolha a opção MySQL Server e o MySQL Workbench.

### 2.2 Configurar o MySQL

1. Defina uma senha para o usuário root (anote para configurar na API).

2. Crie um banco de dados para a API (via MySQL Workbench ou prompt de comando):

```
CREATE DATABASE nice-mg3000-db;
CREATE USER 'nice_user'@'%' IDENTIFIED BY 'senha_segura';
GRANT ALL PRIVILEGES ON nice-mg3000-db.* TO 'nice_user'@'%';
FLUSH PRIVILEGES;
```

3. Se necessário, edite o arquivo my.ini para liberar conexões remotas.


---

## 3️⃣ Selecionar a plataforma da API

### 3.1 Baixar a versão da API

1. Clique em [Releases](https://github.com/nicebrasil/nice-sdk-delivery/releases).
2. Escolha a versão desejada:
   - Any CPU
   - x86
   - x64
3. Baixe o arquivo e descompacte.


---

## 4️⃣ Configurar a API no IIS

### 3.1 Criar um Diretório para a API

Copie o conteúdo da pasta da API que foi descompactada no passo anterior para:

```
C:\inetpub\NiceSDKApi
```

### 3.2 Criar um Aplicativo no IIS

1. Abra o Gerenciador do IIS (inetmgr).

2. Clique com o botão direito em Sites → Adicionar Site.

3. Preencha os campos:
   - Nome do Site: Nice.SDK.Api
   - Caminho Físico: C:\inetpub\NiceSDKApi
   - Endereço IP/Porta: Escolha * e defina a porta (ex: 25002).

4. Clique em OK.

---

## 5️⃣ Configurar o Módulo do ASP.NET Core

1. No IIS, selecione o site Nice.SDK.Api.

2. Vá em Configurações Avançadas e ajuste a Pasta do Aplicativo (C:\inetpub\NiceSDKApi).

3. Em Módulos, verifique se AspNetCoreModuleV2 está ativado.

---

## 6️⃣ Configurar o Banco de Dados na API
Edite o appsettings.json da API e configure a string de conexão do MySQL:

```
"ConnectionStrings": {
  "DefaultConnection": "Server=localhost;Database=nice-mg3000-db;User Id=nice_user;Password=senha_segura;"
}
```

---

## 7️⃣ Configurar Permissões

1. Vá até C:\inetpub\NiceSDKApi.

2. Clique com o botão direito → Propriedades → Segurança.

3. Adicione o usuário IIS_IUSRS e dê permissões Leitura e Execução.

---

## 8️⃣ Criar o `web.config`
Se o arquivo `web.config` não foi gerado, crie manualmente em `C:\inetpub\NiceSDKApi`:

```
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified"/>
    </handlers>
    <aspNetCore processPath="dotnet" arguments="Nice.MG3000.Api.dll" stdoutLogEnabled="true" stdoutLogFile="logs\stdout">
      <environmentVariables>
        <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Production" />
      </environmentVariables>
    </aspNetCore>
  </system.webServer>
</configuration>
```

---

## 9️⃣ Reiniciar o IIS e Testar a API

1. No Prompt de Comando, reinicie o IIS:

```
iisreset
```

2. Teste a API acessando:

    http://localhost:25002/swagger


---

## 🔟 Configurar o Firewall (Se Necessário)
Se a API não estiver acessível externamente, libere a porta no Firewall do Windows:

```
New-NetFirewallRule -DisplayName "API Nice SDK" -Direction Inbound -Protocol TCP -LocalPort 5000 -Action Allow
```

---


## 🔹 Conclusão
Agora sua API Nice.SDK.Api está rodando no IIS com o MySQL configurado! 🚀


---

# 📌 Chave de ativação

## Como utilizar a chave de ativação

1. Para executar os serviços da API é necessário estar de posse da sua chave de ativação.
2. Clique no botão `Authorize`.
3. Preencha a caixa de texto `ApiKey` com o valor da sua chave de ativação.
4. Clique novamente no botão `Authorize` e em seguida `Close`.

![x-api-key](images/x-api-key.png "x-api-key")

> Após inserir a chave de ativação, qualquer acionamento de serviços no Swagger irá enviar a chave no cabeçalho e a API fará a validação da mesma.
> Caso a chave não seja preenchida os métodos não serão executados por falta de autorização.
