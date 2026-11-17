# Arquitetura

## Hub

Java 21, Spring Boot 4.1.2 e Thymeleaf. O catálogo e os guias públicos ficam no classpath. O Hub permanece público e não recebe senhas. `HUB_INVENTORY_URL` define o destino do acesso ao patrimônio; sem esse endereço, o acesso abre a orientação local.

A configuração mantém Nextcloud, repositórios, portal de contas e documentação interna como serviços independentes. O link para um serviço não comprova sua disponibilidade.

## Patrimônio

Java 21, Spring Boot 4.1.2, Spring Security LDAP, Thymeleaf, JDBC e H2. A hierarquia usa uma referência ao registro pai. Espaços ficam na raiz ou dentro de outros locais; móveis, recipientes e itens precisam de um pai. Itens também podem guardar outros itens.

Cada alteração de patrimônio e seu evento de auditoria são gravados na mesma transação H2. Códigos são únicos sem distinção entre maiúsculas e minúsculas. Movimentos não podem criar ciclos. Um local precisa estar vazio para ser excluído.

O mapa apresenta blocos por nível e permite percorrer a hierarquia. A descrição registra posição física. O mapa não usa escala, coordenadas ou medição da planta.

## Identidade

O login usa bind LDAP com a conta do Ramo. Uma conta de leitura pesquisa usuários e grupos. O sistema não compara hashes locais nem mantém contas de autenticação no H2. Após o login, o navegador mantém uma sessão HTTP com cookie HttpOnly e SameSite Strict. A sessão expira após 30 minutos de inatividade.

O servidor consulta novamente os grupos antes de autorizar requisições autenticadas. A revogação de um papel vale para a próxima requisição de uma sessão existente. Falha na consulta bloqueia a requisição. Não há fallback para uma conta local.

Usar o mesmo diretório em vários serviços compartilha contas e senhas; não cria SSO entre os domínios. A sessão do inventário é independente da sessão do portal do LLDAP.

## Administração

Administradores usam a página de usuários para solicitar operações. Somente o servidor chama `/auth/simple/login` e `/api/graphql` do LLDAP com a conta de serviço. Tokens não são encaminhados ao navegador ou gravados no inventário.

O cadastro de contas cria os metadados no diretório. A definição e recuperação de senha permanecem no portal LLDAP. Configure o SMTP do próprio LLDAP caso a implantação utilize recuperação por e-mail.

As operações de diretório são externas e não são atômicas com o banco H2. Uma falha parcial exige conferir o estado atual antes de repetir. O painel registra operações administrativas concluídas e protege contra autoexclusão, auto-revogação administrativa, exclusão da conta de integração e remoção do último administrador identificado.

## Identidade visual

Azul IEEE `#00629B`, marca original, fonte de sistema, superfícies planas e temas claro/escuro. Títulos descrevem a função da página. As métricas são contagens operacionais; os blocos representam o armazenamento físico. Não são necessárias imagens geradas, slogans ou fontes externas para navegar no sistema.
