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

Se precisar de ajuda avançada (como configurar chaves SSH ou restringir acesso), posso elaborar mais detalhes!
