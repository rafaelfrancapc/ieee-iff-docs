# Verificação

Em 17/11/2026:

| Projeto | Testes | Resultado |
| --- | --- | --- |
| Hub | 28 | Sem falhas ou erros |
| Patrimônio | 22 | Sem falhas ou erros |

O Hub verifica páginas, catálogo, busca, conteúdo escapado, configuração, redirecionamentos, identidade visual e o acesso configurável ao patrimônio.

O inventário verifica hierarquia, transações, auditoria, autenticação LDAP por bind, três papéis, sigilo herdado, filtragem no servidor, revogação em sessões existentes, falha segura quando o diretório está indisponível, CSRF e administração de contas pelo contrato GraphQL.

O navegador foi usado para revisar login, mapa, classificação, administração, cadastro de conta fictícia, atribuição de papel, histórico, temas e largura móvel. As capturas estão em `docs/preview` no repositório do inventário e contêm apenas dados de exemplo.

O LDAP de teste usa UnboundID, e a administração GraphQL usa um servidor HTTP simulado. A suíte não altera o LLDAP de produção e não substitui a validação da implantação real com os seus endereços, DN e credenciais. O exemplo Docker deste repositório é alternativo; o ambiente de teste executado é descrito em [Ambiente de teste implantado](AMBIENTE-DE-TESTE.md).

Nenhum DNS, túnel ou serviço externo é criado ao publicar os repositórios. A instalação requer os passos descritos no guia de implantação.
