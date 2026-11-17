# Operação

## Backup

Pare o inventário e copie a pasta `data/` ou o volume Docker correspondente. Preserve permissões e proprietário. Copie também a configuração privada para o cofre/backup adotado pela gestão, sem colocá-la no repositório. O backup do LLDAP deve seguir a instalação do próprio diretório; o H2 do inventário não contém as contas.

Na restauração, mantenha a aplicação parada, restaure os dados e a configuração compatível e só então inicie o serviço. Verifique login, quantidades, hierarquia e classificação. Não execute duas instâncias contra o mesmo arquivo H2.

## Contas e papéis

A diretoria administra o patrimônio; administradores administram contas. O cadastro de uma conta pelo módulo não define sua senha. Conclua a configuração da conta no portal do LLDAP.

Ao remover um papel, as sessões recebem as novas permissões na próxima requisição. Se houver falha no LLDAP, a aplicação bloqueia a requisição enquanto não conseguir verificar os grupos.

A interface protege a conta que está administrando a sessão, o último administrador identificado e a conta de integração. Outras ferramentas administrativas do LLDAP continuam sujeitas às próprias regras; mantenha um processo de recuperação do diretório.

## Histórico

O inventário registra autor, operação, data/hora, origem e destino. A interface mostra os últimos 200 eventos; os demais continuam no banco. A administração mostra os últimos 100 eventos de contas registrados pelo módulo. Eventos criados por outras ferramentas do LLDAP não aparecem automaticamente nesse histórico local.

Falhas parciais na API externa devem ser conferidas no diretório antes de repetir uma alteração de papel. A aplicação não promete transação distribuída entre o LLDAP e o H2.

## Atualização

Compile e execute as suítes antes de substituir o JAR ou a imagem. Faça backup antes de atualizar a base. A migração atual adiciona classificação de sigilo e autor aos dados existentes. Registros anteriores começam liberados aos membros e precisam de revisão de classificação pela diretoria.

## Diagnóstico

- Login recusado: confira usuário, senha, DN, conectividade, conta de leitura e grupos no LLDAP.
- Consulta indisponível após login: a verificação de grupos falhou; confira transporte e disponibilidade do diretório.
- Administração indisponível: confira a origem da API e a conta administrativa de serviço.
- Papel sem efeito: confira o nome do grupo configurado e a associação da conta; a próxima requisição deve refletir a mudança.
- Histórico ou CSV inesperado: confirme o papel e os filtros. Membros recebem apenas o inventário liberado e não recebem movimentações.

Não cole logs com credenciais, cookies, tokens ou itens sigilosos em issues públicas. Use dados fictícios para reproduzir um problema.
