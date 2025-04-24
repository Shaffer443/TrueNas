Para acessar o **TrueNAS CORE** via terminal (linha de comando), você tem duas opções principais:  

### **1. Acesso Direto pelo Console (Físico/Virtual)**
Se você está fisicamente próximo ao servidor ou usando uma interface de virtualização (como VMware, Proxmox, etc.), pode acessar diretamente o console do TrueNAS:

1. **Conecte um teclado e monitor** ao servidor (ou acesse o console virtual se for uma VM).
2. Na tela inicial do TrueNAS, você verá um menu de opções:
   ```
   [1] Configure Network Interface  
   [2] Configure Network Settings  
   [3] Reset Root Password  
   [4] Console Setup  
   [5] Shell  
   [6] Reboot  
   [7] Shutdown  
   ```
3. **Selecione a opção `5) Shell`** para entrar no terminal como `root`.

---

### **2. Acesso Remoto via SSH (Recomendado)**
Se o TrueNAS está em um servidor remoto, o método mais comum é usar **SSH**.

#### **Passo 1: Habilitar o SSH no TrueNAS**
1. Acesse a interface web do TrueNAS (`http://<IP-DO-TRUENAS>`).
2. Vá para **Services > SSH**.
3. Marque **Start Automatically** e clique em **Start** (se ainda não estiver ativo).
4. Configure as opções (recomendado):
   - Permitir login como `root` (se necessário, mas cuidado com segurança).
   - Usar autenticação por chave SSH (mais seguro que senha).

#### **Passo 2: Conectar via SSH**
No seu computador local (Linux/macOS/Windows com Git Bash ou PuTTY), execute:
```sh
ssh root@<IP-DO-TRUENAS>
```
- Substitua `<IP-DO-TRUENAS>` pelo endereço IP do seu servidor.
- Digite a senha do usuário `root` (a mesma usada na interface web).

#### **Exemplo:**
```sh
ssh root@192.168.1.100
```

---

### **Dicas de Segurança**
- **Evite logar como `root` diretamente**: Crie um usuário com permissões `sudo` e desative o login SSH como `root` (em **Services > SSH > Advanced Settings**).
- **Use chaves SSH**: Configure autenticação por chave pública para maior segurança.
- **Firewall**: Certifique-se de que a porta **22 (SSH)** está aberta apenas para IPs confiáveis.

---

### **Problemas Comuns?**
- **"Connection refused"**: Verifique se o serviço SSH está ativo no TrueNAS.
- **Senha incorreta**: Redefina a senha do `root` via console físico (opção `3` no menu inicial).
- **Acesso bloqueado**: Verifique regras de firewall ou configurações de rede.

---

No **TrueNAS CORE**, o usuário **`root`** é o administrador padrão do sistema e tem acesso completo ao terminal. Aqui está como usá-lo corretamente:

---

### **1. Acessando o Terminal como `root`**
#### **Método 1: Via Console Físico/VM**
- Se estiver diretamente no servidor (ou via console de uma VM), selecione a opção **`5) Shell`** no menu inicial do TrueNAS.  
  Você já estará logado como `root`.

#### **Método 2: Via SSH (Acesso Remoto)**
- Conecte-se via SSH usando o comando:
  ```sh
  ssh root@<IP-DO-TRUENAS>
  ```
  - Insira a senha do `root` (a mesma da interface web).

---

### **2. Comandos Básicos como `root`**
Uma vez logado, você pode executar comandos administrativos, como:

| Comando                | Descrição                                  |
|------------------------|-------------------------------------------|
| `zfs list`             | Lista pools/datasets ZFS.                 |
| `zpool status`         | Verifica o estado dos pools de armazenamento. |
| `truenas`             | Abre o menu de configuração do TrueNAS (útil para redefinir rede, etc.). |
| `service sshd restart` | Reinicia o serviço SSH.                   |
| `midclt call system.info` | Exibe informações do sistema (via API). |

⚠️ **Cuidado**: O usuário `root` tem **poder ilimitado**. Comandos errados podem corromper o sistema!

---

### **3. Alternar para `root` a partir de outro usuário**
Se você criou um usuário comum e deseja virar `root` temporariamente:
```sh
su - root
```
- Digite a senha do `root` quando solicitado.

---

### **4. Desabilitar Login Direto como `root` (Recomendado)**
Por segurança, é melhor **evitar logar diretamente como `root`** via SSH. Siga estas etapas:

1. **Crie um usuário administrativo**:
   - Na interface web, vá para **Accounts > Users > Add**.
   - Defina um nome de usuário (ex.: `admin`) e marque **"Permit Sudo"**.

2. **Desative o login `root` via SSH**:
   - Vá para **Services > SSH > Advanced Settings**.
   - Desmarque **"Log in as Root with Password"**.

3. **Acesse via SSH com o novo usuário**:
   ```sh
   ssh admin@<IP-DO-TRUENAS>
   ```
   - Para comandos que exigem `root`, use `sudo`:
     ```sh
     sudo zpool status
     ```

---

### **5. Redefinir a Senha do `root`**
Se você esqueceu a senha:
1. Acesse o **console físico/VM** do TrueNAS.
2. No menu inicial, selecione **`3) Reset Root Password`**.
3. Siga as instruções para definir uma nova senha.

---

### **Dicas Importantes**
- **Registre comandos críticos**: Use `script` para gravar sua sessão (útil para troubleshooting):
  ```sh
  script /root/sessao.log
  ```
- **Backup da configuração**: Sempre exporte os settings da interface web (**System > General > Save Config**) antes de mudanças no terminal.

Se precisar de ajuda com comandos específicos ou configurações avançadas, é só perguntar!
