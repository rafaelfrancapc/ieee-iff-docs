# Ambiente de teste implantado

Este registro descreve o servidor de teste em 17/11/2026. Confirme o estado antes de aplicar mudanças: o exemplo em `deploy/` é uma implantação alternativa, não o Compose ativo neste servidor. Não execute os dois simultaneamente nas mesmas portas.

| Serviço | Endereço público | Destino do Cloudflare Tunnel no host |
| --- | --- | --- |
| HUB | https://hub.ieeeiffcampos.com.br | `http://localhost:8080` |
| LLDAP | https://sso.ieeeiffcampos.com.br | `http://localhost:17170` |
| Nextcloud | https://nuvem.ieeeiffcampos.com.br | `http://localhost:18080` |
| Inventário | https://xn--inventrio-51a.ieeeiffcampos.com.br | `http://localhost:8081` |

O nome do inventário é `inventário.ieeeiffcampos.com.br`; o hostname acima usa sua representação DNS (Punycode). O cloudflared é um serviço do sistema, independente dos contêineres. As quatro portas são vinculadas a `127.0.0.1`; LDAP e banco não são publicados no host. Consulte o painel Cloudflare para conferir as rotas e o DNS.

## Serviços e arquivos

- O conjunto HUB, LLDAP, Nextcloud, MariaDB e cron do Nextcloud é executado a partir de `/root/IEEE/deploy/compose.test.yml`, com `--env-file /root/IEEE/deploy/.env.test`.
- O Inventário é executado a partir de `/root/ieee-iff-patrimonio/compose.yml`, com configuração privada em `/root/ieee-iff-patrimonio/.env`. Sua base H2 fica em volume Docker; `SEED_DEMO=false`.
- O Inventário usa as redes Docker `deploy_backend` e `deploy_edge`. A primeira é interna; a segunda permite a publicação da porta no loopback. O LLDAP está nas duas redes. Proteja o acesso a essas redes: o tráfego LDAP e da API entre contêineres não usa TLS.
- Os arquivos privados contêm credenciais e não devem ser publicados. A interface do LLDAP usa arquivos de tema montados de `/root/IEEE/deploy/lldap-theme/`; ao atualizar a imagem, verifique compatibilidade e restaure a interface padrão se necessário.

## Identidade

A base LDAP é `dc=ieeeiffcampos,dc=com,dc=br`. O Nextcloud usa o servidor `ldap://lldap:3890`, com conta de consulta `nextcloud.bind` no grupo `lldap_strict_readonly`. O filtro de usuários é `(objectClass=person)` e o de login é `(&(objectClass=person)(uid=%uid))`: não há exigência de grupo, inclusive para contas técnicas. O cache LDAP do Nextcloud é de 60 segundos.

O Inventário também usa `nextcloud.bind` para consultas. Para a API administrativa do LLDAP, usa a conta `patrimonio.service`, integrante de `lldap_admin`. Essa conta tem privilégios administrativos no diretório compartilhado; proteger sua credencial e restringir administradores do Inventário é essencial. O grupo `ieee_diretoria` controla edição de patrimônio; não é necessário para consultar itens liberados. Usar a mesma conta em serviços diferentes não fornece SSO.

Não exclua contas de integração. Caso `nextcloud.bind` seja removida, restaure-a com a mesma senha registrada nos arquivos privados de ambos os serviços e associe-a a `lldap_strict_readonly`; então execute `docker exec -u www-data deploy-nextcloud-1 php occ ldap:test-config s01` e teste o login no Inventário. Se a senha for alterada, atualize os dois serviços de forma coordenada. A recriação pode gerar novo identificador LDAP; revise contas mapeadas no Nextcloud sem remover dados às cegas.

## Operação e limites

Os serviços usam reinício automático e volumes persistentes. O Nextcloud tem contêiner de cron. Não há backup ou SMTP configurado por esta implantação: planeje e teste backups e restauração de LLDAP, banco e arquivos do Nextcloud, H2 do Inventário, configuração privada e tema antes de armazenar dados importantes. A recuperação de senha por e-mail depende de SMTP. Revise limites de upload do Cloudflare e fixe versões de imagens testadas antes de uso permanente.

Validação feita no ambiente: HUB e páginas de login responderam, a conexão do Nextcloud ao LDAP foi testada e o Inventário autenticou uma conta do diretório. Teste novamente após mudanças de DNS, credenciais, imagem ou rede.
