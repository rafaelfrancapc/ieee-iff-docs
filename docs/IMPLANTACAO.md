# Implantação com LLDAP

Para o servidor já instalado, consulte [Ambiente de teste implantado](AMBIENTE-DE-TESTE.md). Os passos abaixo descrevem uma implantação alternativa, não os comandos do servidor ativo.

## Preparar as fontes

Clone os três repositórios como pastas irmãs:

```bash
git clone https://github.com/rafaelfrancapc/ieee-iff-hub.git
git clone https://github.com/rafaelfrancapc/ieee-iff-patrimonio.git
git clone https://github.com/rafaelfrancapc/ieee-iff-docs.git
```

Use o LLDAP já implantado. O exemplo não cria um segundo diretório, usuários reais, domínio, DNS ou túnel.

## Preparar o LLDAP

Confirme a base DN real e os endereços internos. As portas padrão do LLDAP são 3890 para LDAP e 17170 para a API/interface web; sua instalação pode usar LDAPS, proxy ou outros endereços.

1. Mantenha uma conta humana em `lldap_admin` para o primeiro acesso administrativo.
2. Crie uma conta dedicada para leitura e associe-a a `lldap_strict_readonly`.
3. Crie uma conta de serviço para a API administrativa e dê a ela as permissões necessárias no LLDAP. A aplicação inteira depende da proteção dessas credenciais.
4. Crie `ieee_diretoria` e associe os diretores, ou atribua esse papel pela página administrativa após o primeiro login.
5. Configure definição/recuperação de senha no portal de contas. E-mail de recuperação depende do SMTP do LLDAP.

Não reutilize senhas de teste. As senhas existentes na suíte automatizada só funcionam nos servidores efêmeros de teste.

## Configurar

Copie `deploy/.env.example` para um arquivo privado e preencha os dados reais. Os grupos padrão são `ieee_diretoria` e `lldap_admin`. Não altere os nomes sem manter o mapeamento e as associações coerentes.

Para Docker, os endereços precisam ser alcançáveis a partir dos contêineres. `localhost` dentro de um contêiner não é a máquina host nem outro contêiner. Use DNS da rede Docker, IP privado ou a origem interna adequada.

O exemplo conjunto exige uma rede Docker externa já existente, compartilhada com o LLDAP, e usa os contextos de build das pastas irmãs. Ajuste `IEEE_NETWORK`, `HUB_SOURCE_DIR` e `INVENTORY_SOURCE_DIR` quando necessário.

```bash
cd ieee-iff-docs/deploy
docker compose --env-file /caminho/privado/ieee.env config --quiet
docker compose --env-file /caminho/privado/ieee.env up --build -d
```

O comando de validação com `--quiet` evita imprimir a configuração expandida com credenciais. Não publique a saída completa de `docker compose config`.

## HTTPS e publicação

Os exemplos publicam Hub e patrimônio somente em loopback, nas portas 8080 e 8081. Configure seu proxy/túnel para destinos separados. O inventário usa rotas a partir de `/`, por isso um subdomínio próprio é mais simples que um prefixo de caminho.

Na implantação com HTTPS, mantenha `SESSION_COOKIE_SECURE=true`. Para desenvolvimento HTTP local, use `false`. O portal de contas pode continuar em `https://sso.ieeeiffcampos.com.br`; o endereço do portal não transforma o login LDAP em SSO.

O Hub usa `HUB_INVENTORY_URL` para o atalho do inventário. O inventário usa `HUB_URL` para retornar ao Hub. Configure os endereços públicos reais somente quando as rotas estiverem disponíveis.

## Primeira verificação

1. Abra o Hub e confira o acesso ao patrimônio.
2. No inventário, confirme o redirecionamento de uma sessão anônima para o login.
3. Entre com um membro e confirme a consulta sem edição.
4. Entre com um diretor e cadastre um item fictício sigiloso dentro de uma sala.
5. Confira que o membro não recebe esse item na busca, árvore, quantidades ou CSV.
6. Confira a página administrativa com uma conta administradora.
7. Revogue o grupo de uma conta de teste e confirme a mudança de acesso na sessão já aberta.
8. Apague somente os dados fictícios de validação que você criou.

A suíte automatizada testa esses contratos com diretórios de teste. Validar a instalação real exige preencher os endereços e credenciais do ambiente; isso não é executado pela publicação dos repositórios.
