Se você criou a pasta via **linha de comando (`mkdir`)**, ela pode não aparecer na interface gráfica do TrueNAS porque o TrueNAS foca em **datasets e volumes ZFS** ao listar o conteúdo da pool.  

### 🔹 Como Resolver:
Se deseja que a pasta apareça na interface do TrueNAS, siga uma das opções abaixo:

---

### ✅ **Opção 1: Criar um Dataset em vez de apenas um diretório**
O TrueNAS trata datasets como unidades separadas dentro da pool, o que facilita gerenciamento, permissões e snapshots.

1. **Acesse o Shell no TrueNAS**  
   Vá até a aba **Shell** no painel ou conecte via SSH.

2. **Crie o Dataset** (substitua `NOME_DA_POOL` e `NOME_DO_DATASET` conforme necessário)
   ```bash
   zfs create NOME_DA_POOL/NOME_DO_DATASET
   ```
3. **Verifique se o Dataset foi criado**  
   ```bash
   zfs list
   ```
4. **Agora a pasta aparecerá na GUI do TrueNAS!** 🎉

---

### ✅ **Opção 2: Criar a Pasta dentro de um Dataset Existente**
Se deseja apenas criar um **diretório comum** dentro de um dataset existente e acessá-lo via SMB/NFS:

1. **Acesse a Pool via Terminal**  
   ```bash
   cd /mnt/NOME_DA_POOL/NOME_DO_DATASET
   ```

2. **Crie o Diretório**  
   ```bash
   mkdir NOVA_PASTA
   ```

3. **Ajuste Permissões (se necessário)**  
   ```bash
   chmod 777 NOVA_PASTA
   ```

4. **Atualize a Interface do TrueNAS**  
   - Se a pasta não aparecer, reinicie o compartilhamento (SMB/NFS) ou o sistema:
     ```bash
     service samba_server restart  # Para SMB
     service nfs-server restart    # Para NFS
     ```

Agora, a pasta criada deve ser visível ao acessar o **dataset via SMB/NFS**, mas **não aparecerá na GUI do TrueNAS**, pois o TrueNAS só exibe datasets, não diretórios comuns.

Se precisar que a pasta apareça na GUI, o melhor caminho é criar um dataset! 🚀
