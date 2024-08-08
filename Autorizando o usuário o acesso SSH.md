# Autorizando Usuario a usar __SSH__ no TrueNAs Core

1. Crie uma chave **SSH Keypairs**
	Na aba Sistema / SSH Keypairs, crie um par de chave publica/privada.

2. Escolha o usuário para usar a chave pública
	Na Accounts / Utilizadores, escolha o ultilizador(usuário) que irá usar a chave para logar via ssh.
	Edite o usuário , no campo __chave publica ssh__ cole o hash da chave publica criada.
	salve.
	
3. Verificar se na aba **Services**, o chave ssh está ativada.
