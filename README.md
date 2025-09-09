# Dinâmica: Design Estratégico do Projeto

## Objetivo
Identificar os subdomínios do projeto, classificá-los (Core, Supporting, Generic) e desenhar os bounded contexts, incluindo suas interações. Esse exercício ajudará a criar uma visão clara e estratégica do domínio.

---

## 1. Nome do Projeto
**AlphaNutri**

---

## 2. Objetivo Principal do Projeto
Conectar nutricionistas, pacientes e produtores de alimentos em uma única plataforma, facilitando consultas, acompanhamento nutricional personalizado e acesso a produtos e ingredientes saudáveis, promovendo hábitos alimentares mais adequados e sustentáveis.

---

## 3. Identificação dos Subdomínios

| **Subdomínio**                      | **Descrição**                                                                                                     | **Tipo**         |
|--------------------------------------|-------------------------------------------------------------------------------------------------------------------|------------------|
| Gestão de Consultas Nutricionais     | Gerencia agendamento, realização e acompanhamento das consultas nutricionais, incluindo histórico e planos.     | Core Domain      |
| Plano Alimentar Personalizado        | Criação, edição e acompanhamento de planos alimentares baseados nas necessidades e restrições do paciente.       | Core Domain      |
| Marketplace de Produtos Alimentícios | Conecta produtores de alimentos saudáveis a pacientes e nutricionistas, permitindo compra direta na plataforma.  | Core Domain      |
| Cadastro e Gestão de Usuários        | Registro, autenticação, permissões e perfis de nutricionistas, pacientes e produtores.                           | Supporting       |
| Pagamentos e Faturamento             | Processamento de pagamentos por consultas, planos e compras, incluindo repasse a nutricionistas e produtores.    | Generic          |
| Comunicação e Notificações           | Envio de mensagens, alertas e notificações para consultas, pedidos e atualizações de planos.                     | Supporting       |
| Relatórios e Análises                 | Geração de relatórios sobre evolução dos pacientes, métricas de vendas e desempenho de nutricionistas/produtores.| Supporting       |

---


## 6. Definição da Linguagem Ubíqua

| **Termo**             | **Descrição**                                                                                   |
|-----------------------|-------------------------------------------------------------------------------------------------|
| Consulta Nutricional  | Sessão entre nutricionista e paciente para avaliação e orientação alimentar.                    |
| Plano Alimentar       | Documento digital com refeições, porções e frequência, baseado nas necessidades do paciente.    |
| Paciente              | Usuário que recebe acompanhamento nutricional e/ou compra produtos no marketplace.              |
| Nutricionista         | Profissional de saúde habilitado para criar planos e prestar atendimento.                        |
| Produtor              | Fornecedor de alimentos saudáveis na plataforma.                                                 |
| Marketplace           | Ambiente digital para oferta e venda de produtos alimentícios.                                   |
| Pedido                | Transação de compra de produtos no marketplace.                                                  |
| Consulta Finalizada   | Status que indica conclusão do atendimento e entrega das recomendações.                          |
| Pagamento Processado  | Confirmação de que o valor foi recebido e repassado ao destinatário.                              |

---

## 7. Estratégia de Desenvolvimento

| **Subdomínio**                      | **Estratégia**                                     | **Ferramentas ou Serviços (se aplicável)**              |
|--------------------------------------|---------------------------------------------------|---------------------------------------------------------|
| Gestão de Consultas Nutricionais     | Desenvolvimento interno                           | -                                                       |
| Plano Alimentar Personalizado        | Desenvolvimento interno                           | -                                                       |
| Marketplace de Produtos Alimentícios | Desenvolvimento interno com futuras integrações   | Integração logística/pagamento                          |
| Cadastro e Gestão de Usuários        | Interno com autenticação segura                   | Auth0, Keycloak                                         |
| Pagamentos e Faturamento             | Terceirizar usando APIs de pagamento               | Stripe, PayPal, Mercado Pago                            |
| Comunicação e Notificações           | Interno com serviços de mensageria                 | Firebase Cloud Messaging, Twilio                        |
| Relatórios e Análises                 | Interno com possibilidade de integração de BI     | Metabase, Power BI Embedded                             |

---

## 8. Bounded Context

- **User**  
  Responsável por identidades e perfis de usuários.  

- **Nutrition Plan**  
  Gestão de planos de nutrição, consultas e assinaturas.  

- **Seller Management**  
  Cadastro e atributos dos sellers (fornecedores/parceiros).  

- **Product Catalog**  
  Catálogo de produtos, preços e restrições.  

- **Ordering**  
  Fluxo de pedidos: criação, atualização e status de ordens.  

- **Billing & Payment**  
  Cobranças, pagamentos e integração com PSPs externos.  

- **Notifications & Comms**  
  Envio de mensagens e notificações (event-driven).


| **Origem**         | **Destino**                   | **Tipo de Relacionamento**             | **Explicação**                                                                  |
|--------------------|-------------------------------|----------------------------------------|---------------------------------------------------------------------------------|
| User               | Todos                         | Customer–Supplier + Shared Kernel mínimo | Fornece identidades/perfis; compartilhar só tipos estáveis (UserId, Email).   |
| Seller Management  | Product Catalog               | Customer–Supplier                      | Cadastro/atributos de sellers alimentam o catálogo.                             |
| Product Catalog    | Ordering                      | Customer–Supplier                      | Ordering depende de itens, preços e restrições do catálogo.                     |
| Ordering           | Billing & Payment             | Customer–Supplier                      | Ordering solicita autorizações/capturas; Payments responde com status interno.  |
| Billing & Payment  | PSP externo                   | Anti-Corruption Layer                  | Tradução de status/erros do provedor para o modelo interno (protege o domínio). |
| Nutrition Plan     | Notifications & Comms         | Customer–Supplier (event-driven)       | Eventos PlanCreated/PlanUpdated disparam mensagens ao paciente.                 |
| Ordering           | Notifications & Comms         | Customer–Supplier (event-driven)       | Eventos OrderPlaced/OrderPaid/OrderFulfilled geram notificações.                |
| Nutrition Plan     | Billing & Payment (plano pago) | Customer–Supplier                     | Dispara cobranças de assinatura/sessão quando aplicável.                        |

