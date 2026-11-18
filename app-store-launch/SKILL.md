---
name: app-store-launch
description: Roteiro para tirar um app da ideia até a App Store da Apple — validação, jurídico, design, desenvolvimento, qualidade, submissão e lançamento — com etapas, prazos, gargalos típicos e pontos de melhoria. Use SEMPRE que o usuário quiser criar um app para iPhone/iOS, publicar na App Store, montar um cronograma ou roadmap de app, preparar uma submissão para revisão da Apple, entender por que um app foi rejeitado, ou acompanhar o andamento de um projeto de app (status, atrasos, gargalos). Também use quando o usuário pedir uma estratégia de negócio enxuta para um app de serviços (marketplace, agendamento, mão de obra temporária).
---

# Do zero à App Store

Este roteiro serve para apps de serviço com orçamento baixo. O princípio é gastar
dinheiro onde está o risco: primeiro provar que alguém paga, depois resolver o
jurídico, só então programar.

## Fases e prazos de referência (equipe de 1 a 3 pessoas)

| Fase | Duração | Sai daqui com |
|---|---|---|
| 1. Validação | 2–4 sem | 15+ entrevistas, lista de espera, 3 clientes piloto com pré-pagamento |
| 2. Jurídico e operação | 3–6 sem (em paralelo) | Modelo de contratação definido, contratos, termos, política LGPD |
| 3. Produto e design | 2–3 sem | Protótipo no Figma testado com 5 usuários |
| 4. Desenvolvimento | 5–7 sem | Build interno com os fluxos principais |
| 5. Qualidade | 3–4 sem (sobreposta) | Testes automatizados, monitoramento, beta no TestFlight com clientes reais |
| 6. App Store | 1–2 sem | Ficha da loja completa e app aprovado |
| 7. Lançamento e fidelização | contínuo | Clientes pagantes, métricas semanais, ajuste de preço |

Total típico: 16 a 22 semanas. Recesso de fim de ano e Carnaval no Brasil
costumam custar 1–2 semanas; coloque isso no cronograma.

## Gargalos que mais atrasam (resolva cedo)

1. **Conta Apple de empresa exige número D-U-N-S.** Pode levar semanas. Peça na
   primeira semana. Conta pessoal é mais rápida, mas mostra seu nome na loja.
2. **Jurídico decide o produto.** Em serviços de mão de obra temporária no Brasil,
   a Lei 6.019/74 exige empresa de trabalho temporário registrada; intermediar
   autônomos/PJ tem risco de vínculo. Parecer antes de programar.
3. **Gateway de pagamento com split** (Asaas, Pagar.me, Stripe) pede documentos e
   homologação. Abra a conta semanas antes de integrar.
4. **Rejeições comuns na revisão da Apple** (confira sempre a versão vigente das
   App Store Review Guidelines):
   - 5.1.1: falta opção de excluir a conta dentro do app.
   - 4.2: app que é só um site embrulhado, sem funcionalidade própria.
   - 2.1: revisor sem login de teste ou com fluxo quebrado. Deixe credenciais e
     instruções nas notas da revisão.
   - 4.8: login social sem oferecer Sign in with Apple (ou outra opção equivalente).
   - 5.1.2 / rótulos de privacidade inconsistentes com o que o app coleta.
5. **Pagamento por serviço físico/humano** consumido fora do app não usa compra
   dentro do app (diretriz 3.1.5). Não aplique IAP nesses casos; isso evita a
   comissão da Apple sobre o serviço.

## Stack de baixo custo

- App: Expo + React Native (siga a skill `mobile-app-bootstrap`), ou FlutterFlow
  se ninguém programa.
- Backend: Supabase (banco, login, arquivos, regras de acesso).
- Pagamento: gateway brasileiro com split automático.
- Builds: EAS Build ou Xcode Cloud; distribuição de teste pelo TestFlight.
- Custos fixos mínimos: Apple Developer US$ 99/ano; o resto tem plano gratuito
  para começar.

## Ferramentas visuais de qualidade e acompanhamento

- **Painel de roadmap ao vivo** (etapas, status, gargalos, linha do tempo): um
  artifact compartilhado no claude.ai, ou GitHub Projects / Linear / Jira.
- **Testes de ponta a ponta**: Maestro (fluxos em YAML, fácil de ler, grava vídeo).
- **CI**: GitHub Actions rodando testes a cada pull request.
- **Falhas em produção**: Sentry ou Firebase Crashlytics.
- **Beta**: TestFlight (até 10 mil testadores, feedback com captura de tela).
- **Pós-lançamento**: App Store Connect Analytics (downloads, retenção, falhas).

Skills úteis em cada fase:
- Validação: `design:user-research`, `design:research-synthesis`
- Design: `design:design-critique`, `design:accessibility-review`, `design:ux-copy`
- Arquitetura: `engineering:system-design`, `engineering:architecture`
- Qualidade: `engineering:testing-strategy`, `engineering:code-review`
- Submissão: `engineering:deploy-checklist`
- Acompanhamento: `engineering:standup` (resumo semanal), `/loop` para checagens
  recorrentes, `finance:variance-analysis` para previsto x realizado

## Como acompanhar o projeto

Para cada etapa registre: responsável, início, fim, status (a fazer, em andamento,
bloqueada, concluída), progresso %, gargalo e ponto de melhoria. Toda semana:
1. Liste etapas atrasadas (fim vencido sem conclusão) e bloqueadas.
2. Para cada uma, decida: destravar, replanejar a data ou cortar escopo.
3. Compare o progresso ponderado pela duração com o tempo decorrido.

## Estratégia de negócio enxuta (apps de serviço)

- Comece por **um nicho e uma cidade**. Generalizar cedo dilui a oferta.
- Cobre de forma simples: avulso por hora + plano mensal de reserva.
- Seja transparente na divisão (ex.: 80% profissional / 20% plataforma) e pague o
  profissional rápido (D+2). Justiça percebida é retenção dos dois lados.
- Fidelize com algo que fica no app: histórico, preferências, "manual da função",
  profissional fixo. Trocar de fornecedor deve significar recomeçar.
- Métricas semanais: tempo para atender um pedido, % de pedidos atendidos,
  recompra em 60 dias, margem por hora após taxas, avaliação média dos dois lados.
