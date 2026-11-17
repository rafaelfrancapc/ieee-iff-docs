# Papéis e sigilo

| Operação | Membro | Diretoria | Administrador |
| --- | --- | --- | --- |
| Consultar patrimônio liberado | Sim | Sim | Sim |
| Consultar patrimônio sigiloso | Não | Sim | Sim |
| Cadastrar, editar, mover ou excluir | Não | Sim | Sim |
| Consultar movimentações | Não | Sim | Sim |
| Exportar inventário visível | Sim | Sim | Sim |
| Gerenciar contas e papéis | Não | Não | Sim |

## Grupos

- Toda conta autenticada no LLDAP recebe `MEMBER`.
- O grupo `ieee_diretoria` concede `DIRECTOR`.
- O grupo `lldap_admin` concede `ADMIN` e `DIRECTOR`.

`LLDAP_DIRECTOR_GROUP` e `LLDAP_ADMIN_GROUP` permitem ajustar os nomes. O padrão `lldap_admin` é o grupo administrativo do LLDAP, com efeitos nos serviços que usam esse diretório. A troca de papel altera apenas os grupos mapeados pelo inventário e preserva os outros grupos da conta.

A primeira conta administradora deve existir no LLDAP antes de entrar no módulo. O sistema não cria um administrador automaticamente. O grupo da diretoria pode ser criado no portal do LLDAP ou pela primeira atribuição de diretoria feita por um administrador do módulo.

## Classificação

No cadastro ou edição de um local/item, **Membros do Ramo** libera a consulta para qualquer conta autenticada. **Sigiloso** limita a consulta à diretoria e aos administradores.

Um item dentro de um local sigiloso herda a restrição, mesmo que seu cadastro individual esteja liberado aos membros. Um item individualmente sigiloso continua restrito dentro de um local liberado.

Ao mover um item que herdou sigilo para fora de um local restrito, o servidor preserva seu sigilo como classificação própria. Para liberar a consulta depois, um editor precisa alterar explicitamente a classificação.

## Respostas e histórico

A API remove registros sigilosos e seus descendentes antes de entregar os dados a membros. Busca, árvore, contagens e CSV usam somente essa resposta visível. Não é apenas uma regra de esconder botões no navegador.

O histórico fica restrito à diretoria e aos administradores porque pode conter nomes e caminhos anteriores, incluindo registros que já foram excluídos. O histórico administrativo de contas é exclusivo dos administradores.

## Implantação e revisão

A migração de uma base antiga mantém seus registros e os classifica inicialmente como liberados aos membros. Revise a classificação antes de abrir o acesso a uma base existente. Nenhuma classificação pode retirar informações que um usuário já recebeu legitimamente em uma consulta anterior.

Mudanças de grupo passam a valer na próxima requisição. Senhas, recuperação e disponibilidade do diretório pertencem à implantação do LLDAP. Use HTTPS para o inventário e transporte protegido ou rede privada para comunicar com o diretório.
