# Introdução ao AWS IAM — políticas de senha, usuários, grupos e testes de permissões

> Laboratório prático para dominar o **AWS Identity and Access Management (IAM)**: política de senhas em nível de conta, análise de usuários/grupos, políticas gerenciadas vs. inline e validação de permissões no console (S3 e EC2).

---

##  Objetivos do laboratório

Ao final, você será capaz de:

- Criar e aplicar **política de senhas** em nível de conta;
- Explorar **usuários** e **grupos** pré-criados;
- Inspecionar **políticas IAM** (gerenciadas e inline);
- Atribuir **usuários a grupos** de acordo com suas funções;
- Localizar e usar o **URL de login** do IAM;
- **Testar permissões** acessando S3/EC2 como cada usuário.

>  Duração estimada: ~60 minutos

---

##  Contexto

Ambientes corporativos exigem **controle de acesso mínimo necessário (least privilege)** e **autenticação consistente**. O **IAM** provê identidades (usuários, funções), credenciais (senhas, chaves, MFA) e **políticas** que determinam *quem* pode fazer *o quê* em *quais recursos*.

---

##  Arquitetura lógica e personas

Trabalhamos com três personas IAM e três grupos com escopos distintos:

| Usuário  | Grupo        | Permissões principais                       |
|----------|--------------|---------------------------------------------|
| `user-1` | `S3-Support` | **Somente leitura** no **Amazon S3**        |
| `user-2` | `EC2-Support`| **Somente leitura** no **Amazon EC2**       |
| `user-3` | `EC2-Admin`  | **Describe/Start/Stop** em instâncias EC2   |

Grupos e políticas:

- **S3-Support** → política **gerenciada** `AmazonS3ReadOnlyAccess`
- **EC2-Support** → política **gerenciada** `AmazonEC2ReadOnlyAccess`
- **EC2-Admin** → política **inline** (cliente) com ações `Describe`, `StartInstances`, `StopInstances` para EC2

> Políticas **gerenciadas**: pré-definidas e reutilizáveis.  
> Políticas **inline**: ligadas a um **único** usuário ou grupo, úteis para exceções controladas.

---

##  Passo a passo executado

### 1) Política de senhas (Account settings)
- **Comprimento mínimo**: 10
- **Complexidade**: habilitar (maiúscula, minúscula, número, caractere especial)
- **Expiração**: 90 dias
- **Reuso**: impedir reutilização de 5 senhas

> Resultado: reforço da postura de segurança em **toda a conta**.

---

### 2) Explorar usuários e grupos
- Usuários pré-criados: `user-1`, `user-2`, `user-3`
- Grupos pré-criados: `EC2-Admin`, `EC2-Support`, `S3-Support`
- Inspeção de políticas:
  - `AmazonEC2ReadOnlyAccess` (listar/visualizar EC2, ELB, CloudWatch, Auto Scaling)
  - `AmazonS3ReadOnlyAccess` (listar/obter S3)
  - **Inline** `EC2-Admin-Policy` (Describe/Start/Stop EC2)
    
---

### 3) Atribuir usuários aos grupos
- user-1 → S3-Support
- user-2 → EC2-Support
- user-3 → EC2-Admin
- Valide no console (lista de grupos) se cada um aparece com 1 usuário associado.
  
---

### 4) Login IAM e testes de permissão
- Recupere o IAM sign-in URL da conta (ex.: https://<ID>.signin.aws.amazon.com/console).
- Use janela privada/anônima para testar cada usuário:

##### Teste com user-1 (S3-Support)

- Consegue listar buckets e navegar no S3
- Ao acessar EC2 → mensagem “You are not authorized…”

##### Teste com user-2 (EC2-Support)

- Consegue visualizar instâncias EC2 (readonly)
- Ao tentar Stop/Start → falha por permissão
- Ao acessar S3 → sem permissão para listar buckets

##### Teste com user-3 (EC2-Admin)

- Consegue visualizar e Stop/Start instâncias EC2
- Use a região correta caso a lista não apareça

---
## Resultados:
- Política de senhas endurecida em nível de conta
- Governança por grupos (RBAC) e políticas adequadas
- Validação prática do princípio de least privilege
- Diferenciação clara entre **pol
  
---
## 📘 Descrição Detalhada

Este laboratório teve como objetivo apresentar e consolidar o entendimento sobre o serviço **AWS Identity and Access Management (IAM)** — a base da segurança e governança em qualquer ambiente AWS. Ele permitiu compreender como o controle de identidade, autenticação e autorização funciona na nuvem e como implementar políticas de acesso seguras e eficientes dentro de uma organização.

O **IAM** é o serviço da AWS responsável por **gerenciar usuários, grupos, funções e políticas de permissões**, determinando quem pode acessar quais recursos e de que forma. Em um ambiente corporativo, isso é essencial para aplicar o princípio de **Least Privilege (privilégio mínimo)**, garantindo que cada colaborador ou sistema tenha apenas as permissões estritamente necessárias para realizar suas funções.

Logo no início do laboratório, foi configurada uma **política de senhas** em nível de conta, com parâmetros de segurança aprimorados, como comprimento mínimo de **10 caracteres**, exigência de **caracteres especiais**, **expiração a cada 90 dias** e **bloqueio de reutilização de senhas**. Essa etapa ilustra como a AWS permite **centralizar políticas corporativas de autenticação**, fortalecendo a segurança organizacional e evitando acessos indevidos.

Em seguida, foi feita uma **exploração dos usuários e grupos de usuários pré-criados**. Três usuários foram disponibilizados — `user-1`, `user-2` e `user-3` —, e três grupos representando funções distintas dentro da empresa — `S3-Support`, `EC2-Support` e `EC2-Admin`.  
Essa estrutura reflete um cenário realista de **segregação de funções (SoD — Segregation of Duties)**, em que cada equipe tem permissões diferentes conforme sua responsabilidade operacional.

Durante essa análise, foram observadas as diferenças entre **políticas gerenciadas** e **políticas inline**:

- As **políticas gerenciadas** são pré-definidas e podem ser aplicadas a múltiplos usuários ou grupos. No laboratório, os grupos `S3-Support` e `EC2-Support` receberam as políticas **AmazonS3ReadOnlyAccess** e **AmazonEC2ReadOnlyAccess**, respectivamente. Essas políticas permitem visualizar recursos, mas não alterá-los — o que é ideal para funções de suporte técnico.  
- Já o grupo `EC2-Admin` possuía uma **política inline**, criada manualmente, que concedia permissões específicas para **listar, iniciar e interromper instâncias EC2**. Esse tipo de política é aplicado diretamente a um único grupo ou usuário e é útil para casos pontuais que exigem controle mais granular.

Após compreender as permissões e grupos existentes, o laboratório seguiu para a **atribuição de usuários aos grupos correspondentes**:

- `user-1` foi adicionado ao grupo **S3-Support**, com acesso de leitura ao S3;  
- `user-2` foi adicionado ao grupo **EC2-Support**, com acesso de leitura ao EC2;  
- `user-3` foi adicionado ao grupo **EC2-Admin**, com permissão para iniciar e interromper instâncias EC2.  

Essa etapa reforça o uso de **grupos de usuários como mecanismo de controle de acesso baseado em funções (RBAC — Role-Based Access Control)**, o que torna a administração de permissões mais organizada, escalável e menos suscetível a erros humanos.

Posteriormente, foi testado o **URL de login do IAM**, uma característica fundamental para ambientes multiusuário e empresariais. Esse link exclusivo permite que colaboradores da organização acessem o Console de Gerenciamento da AWS com as credenciais IAM designadas, garantindo uma autenticação separada da conta raiz. Esse mecanismo é essencial para a implementação de políticas de segurança robustas, auditoria e rastreabilidade de ações (via **AWS CloudTrail**).

Com o ambiente configurado, foi executada a parte mais prática do laboratório: **testar os logins e permissões de cada usuário**.  
Utilizando janelas privadas de navegador, foram realizadas as autenticações com cada conta e observados os resultados esperados:

- **user-1 (S3-Support):** conseguiu listar buckets e acessar o conteúdo dos objetos no **Amazon S3**, mas não conseguiu visualizar nem interagir com o **Amazon EC2**, confirmando a aplicação correta da política de leitura no S3.  
- **user-2 (EC2-Support):** conseguiu visualizar instâncias EC2 e seus estados, mas ao tentar interromper ou iniciar uma instância, recebeu a mensagem _“You are not authorized to perform this operation”_. Esse comportamento demonstra o efeito de políticas de somente leitura. Além disso, `user-2` não conseguiu acessar o S3.  
- **user-3 (EC2-Admin):** pôde visualizar e interromper instâncias EC2, validando que a política inline concedia permissões de alteração. Essa é uma função administrativa controlada e, portanto, deve ser atribuída apenas a usuários de confiança ou com necessidade comprovada.

Esses testes demonstraram, de forma prática, como o IAM aplica políticas em tempo real e como diferentes identidades podem ter **níveis de acesso completamente distintos** dentro da mesma conta. Isso reforça o conceito de **governança de acesso**, um dos pilares do **AWS Well-Architected Framework (pilar de Security)**.

Além da parte técnica, o laboratório evidenciou **boas práticas de segurança**, como:

- Evitar o uso de credenciais da **conta raiz**;  
- Implementar **políticas de senha fortes**;  
- **Agrupar usuários** conforme papéis (funções) e não individualmente;  
- **Revisar e auditar permissões** periodicamente;  
- Garantir **autenticação centralizada com MFA (autenticação multifator)** sempre que possível.  

Ao final, foi possível compreender que o **IAM não é apenas um serviço técnico**, mas sim uma **camada estratégica de gestão de identidade e conformidade corporativa**. Ele é essencial para proteger ativos, atender a requisitos de auditoria e permitir o crescimento sustentável das operações em nuvem com segurança e controle.

O aprendizado adquirido com este laboratório serve de base para o uso de serviços avançados, como **AWS Organizations** (gestão multi-conta), **IAM Roles** (para delegação entre serviços), **IAM Identity Center (SSO)** (para autenticação centralizada), e **AWS Policy Simulator**, ferramenta que auxilia na verificação e depuração de políticas complexas.

Concluindo, este exercício reforçou o domínio dos seguintes **pilares da segurança em nuvem**:

1. **Autenticação:** criação e validação de identidades;  
2. **Autorização:** concessão de permissões baseadas em políticas;  
3. **Auditoria:** rastreabilidade e monitoramento de acessos.  

Em suma, este laboratório não apenas apresentou o funcionamento do IAM, mas também demonstrou como sua aplicação prática garante **segurança, eficiência administrativa e conformidade corporativa** em qualquer ambiente AWS moderno.
