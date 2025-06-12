Error: 13.2-RELEASE was not found

Esse erro ocorre quando o **TrueNAS CORE** não consegue baixar a versão específica do sistema (**13.2-RELEASE**) necessária para criar a **Jail** do Nextcloud. Isso pode acontecer por vários motivos, mas geralmente está relacionado a problemas de conexão, espelhos (mirrors) offline ou a versão do FreeBSD não estar mais disponível nos repositórios padrão.

### **Soluções Possíveis:**

#### **1. Verificar e Atualizar os Espelhos (Mirrors) do iocage**
O iocage (sistema de gerenciamento de Jails) depende de servidores espelho para baixar as versões do FreeBSD. Se o espelho padrão estiver offline, você pode definir um alternativo.

- **No Shell do TrueNAS**, execute:
  ```sh
  iocage fetch
  ```
  - Isso listará os mirrors disponíveis. Escolha um diferente (como `ftp.freebsd.org` ou outro).

#### **2. Forçar o Download da Release 13.2-RELEASE**
Se o `iocage fetch` não resolver, tente baixar manualmente:
```sh
iocage fetch -r 13.2-RELEASE
```
Ou especifique um mirror manualmente:
```sh
iocage fetch -r 13.2-RELEASE -n ftp.freebsd.org
```

#### **3. Usar uma Versão Mais Recente (Recomendado)**
Se a **13.2-RELEASE** estiver realmente indisponível, você pode tentar uma versão mais nova compatível com seu TrueNAS CORE:
```sh
iocage fetch
```
- Escolha a versão mais recente listada (ex.: `13.3-RELEASE` ou `13.4-RELEASE`).

#### **4. Verificar se o TrueNAS Está Atualizado**
- Certifique-se de que seu **TrueNAS CORE** está na versão mais recente (vá para **System → Update** na interface web).
- Versões muito antigas do TrueNAS podem ter incompatibilidades com os repositórios atualizados do FreeBSD.

#### **5. Instalar o Nextcloud via Plugin (Alternativa Simples)**
Se a instalação manual estiver complicada, tente usar o **plugin oficial do Nextcloud**:
1. Vá para **Plugins** na interface web.
2. Busque por `Nextcloud` e instale.
3. O TrueNAS cuidará automaticamente da Jail e das dependências.

### **Se Nada Funcionar...**
- **Verifique sua conexão com a internet** (o TrueNAS precisa de acesso aos servidores do FreeBSD).
- **Reinicie o serviço iocage**:
  ```sh
  service iocage restart
  ```
- **Consulte os logs** para mais detalhes:
  ```sh
  tail -f /var/log/messages
  ```

