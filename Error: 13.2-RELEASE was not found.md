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
  ---

  Entendi o problema - mesmo tendo a pasta `13.5-RELEASE` em `/mnt/Dados/iocage/releases/`, o iocage não está reconhecendo essa release. Vamos resolver isso:

### Solução Definitiva:

1. **Verifique a estrutura da release**:
```bash
ls -l /mnt/Dados/iocage/releases/13.5-RELEASE/
```
   - Deve conter pelo menos:
     ```
     base.txz
     root/
     MANIFEST
     ```

2. **Se estiver incompleta, recrie a release**:
```bash
cd /mnt/Dados/iocage/releases/
rm -rf 13.5-RELEASE
iocage fetch -r 13.5-RELEASE -n ftp.freebsd.org
```

3. **Corrija manualmente se necessário**:
```bash
mkdir -p /mnt/Dados/iocage/releases/13.5-RELEASE/root
cd /mnt/Dados/iocage/releases/13.5-RELEASE
fetch https://download.freebsd.org/ftp/releases/amd64/13.5-RELEASE/base.txz
tar -xvf base.txz -C root
```

4. **Crie um arquivo MANIFEST**:
```bash
cat > MANIFEST <<EOF
{
  "arch": "amd64",
  "boot": "no",
  "files": [
    {"name": "base.txz", "size": "$(stat -f %z base.txz)"}
  ],
  "name": "13.5-RELEASE",
  "os_version": "1300513"
}
EOF
```

5. **Tente criar a jail novamente**:
```bash
iocage create -r 13.5-RELEASE -n nextcloud ip4_addr="vnet0|192.168.1.60/24" defaultrouter="192.168.1.1" vnet=on allow_raw_sockets=1 boot=on
```

### Alternativa (usar outra release):
Se persistir o erro, use a 13.4-RELEASE que já existe:
```bash
iocage create -r 13.4-RELEASE -n nextcloud ip4_addr="vnet0|192.168.1.60/24" defaultrouter="192.168.1.1" vnet=on allow_raw_sockets=1 boot=on
```

### Dica Crucial:
Verifique sempre os logs detalhados:
```bash
tail -f /var/log/iocage.log
```

Se ainda falhar, compartilhe a saída completa de:
```bash
ls -R /mnt/Dados/iocage/releases/13.5-RELEASE/
```

---

Ótimo! Parece que você conseguiu criar a jail **nextcloud** com sucesso, apesar de alguns detalhes importantes que precisamos ajustar. Vamos organizar tudo:

---

### **Próximos Passos para Configurar o Nextcloud**

#### 1. **Corrigir o Nome da Jail (Se Aplicável)**
No seu comando, você digitou `locage` em vez de `iocage`. Verifique se a jail foi realmente criada:
```bash
iocage list
```
Se não aparecer, crie novamente com o comando correto:
```bash
iocage create -r 13.5-RELEASE -n nextcloud ip4_addr="vnet0|192.168.1.60/24" defaultrouter="192.168.1.1" vnet=on allow_raw_sockets=1 boot=on
```

---

#### 2. **Acessar a Jail**
```bash
iocage console nextcloud
```

---

#### 3. **Dentro da Jail, Instalar o Nextcloud**
Execute os seguintes comandos **dentro da jail**:
```bash
# Atualize os pacotes
pkg update && pkg upgrade -y

# Instale as dependências
pkg install -y nextcloud-php82 mysql57-server nginx

# Configure o MySQL
sysrc mysql_enable=YES
service mysql-server start
mysql_secure_installation
```

---

#### 4. **Configurar o Nextcloud**
```bash
# Crie o diretório para o Nextcloud
mkdir -p /usr/local/www/nextcloud
chown -R www:www /usr/local/www/nextcloud

# Configure o Nginx (exemplo básico)
cat > /usr/local/etc/nginx/nginx.conf <<EOF
worker_processes  1;
events {
    worker_connections  1024;
}
http {
    include       mime.types;
    default_type  application/octet-stream;
    sendfile        on;
    keepalive_timeout  65;
    server {
        listen       80;
        server_name  _;
        root /usr/local/www/nextcloud;
        index index.php;
        location ~ \.php$ {
            fastcgi_pass   127.0.0.1:9000;
            fastcgi_index  index.php;
            fastcgi_param  SCRIPT_FILENAME \$document_root\$fastcgi_script_name;
            include        fastcgi_params;
        }
    }
}
EOF

# Inicie o Nginx
sysrc nginx_enable=YES
service nginx start
```

---

#### 5. **Acessar o Nextcloud via Navegador**
Abra no navegador:
```
http://192.168.1.60
```
Siga o assistente de instalação do Nextcloud.

---

### **Problemas Comuns e Soluções**

#### **Se a Jail Não Aparecer no `iocage list`**
- Verifique se o nome está correto:
  ```bash
  iocage list -l
  ```
- Se necessário, destrua e recrie:
  ```bash
  iocage destroy nextcloud -f
  iocage create -r 13.5-RELEASE -n nextcloud ip4_addr="vnet0|192.168.1.60/24" defaultrouter="192.168.1.1" vnet=on allow_raw_sockets=1 boot=on
  ```

#### **Se o Nextcloud Não Carregar**
- Verifique os logs do Nginx:
  ```bash
  tail -f /var/log/nginx/error.log
  ```
- Confira se o PHP está rodando:
  ```bash
  service php-fpm start
  ```

---

### **Resumo dos Comandos Úteis**
| Comando | Descrição |
|---------|-----------|
| `iocage list` | Lista todas as jails |
| `iocage console nextcloud` | Acessa a jail do Nextcloud |
| `service nginx restart` | Reinicia o Nginx |
| `mysql -u root -p` | Acessa o MySQL |

Se precisar de ajuda com algum passo específico, me avise!

