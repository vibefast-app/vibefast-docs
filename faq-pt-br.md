# Perguntas Frequentes

[English](./faq.md) · [繁中](./faq-zh.md) · [日本語](./faq-jp.md) · [Español](./faq-es.md) · [Português (BR)](./faq-pt-br.md)

-----

## Sobre o produto

### O vibefast.app é open source?

Não. O vibefast.app template é um produto comercial. Este repositório público serve apenas como introdução e ponto de entrada para recursos de aprendizagem.

### Qual é a diferença entre o repositório público e o privado?

O repositório público contém a introdução do produto e tutoriais de Vibe Coding. O repositório privado contém o código-fonte completo e toda a documentação para compradores — entregue após a compra.

### Posso usar o vibefast.app template para meu próprio produto?

Sim — esse é o caso de uso principal. A maioria dos compradores faz o rebrand diretamente como seu próprio produto, ou usa como base para um SaaS ou web app personalizado.

### Preciso manter todos os módulos padrão?

Não. O vibefast.app template é entregue como um produto de referência completo, mas você pode manter, ajustar ou remover módulos conforme sua necessidade. Se não precisa do blog, por exemplo, pode removê-lo sem afetar nada mais.

### Todos os detalhes técnicos serão publicados aqui?

Não. Este repositório cobre apenas introduções e tutoriais públicos. A documentação completa de arquitetura, guias de deploy e a referência de API estão dentro do repositório privado.

-----

## Sobre a compra

### Como eu compro? Como recebo o código após o pagamento?

Acesse [vibefast.app](https://vibefast.app) e complete o pagamento via Stripe. Após o pagamento, o sistema envia automaticamente um convite do GitHub para seu email. Aceite o convite para acessar o repositório privado. Todo o processo é automatizado e normalmente leva apenas alguns minutos.

Se não receber o convite após 10 minutos, verifique sua pasta de spam ou entre em contato com [hello@dankoai.com](mailto:hello@dankoai.com).

### O preço early bird é por tempo limitado?

Sim. O preço de $99 está disponível até 1º de junho de 2026, depois sobe para $199. Comprar agora versus comprar após 1º de junho é uma diferença de $100.

### O que o pagamento único inclui?

$99 inclui: acesso ao repositório privado, o código-fonte completo, toda a documentação para compradores e todas as atualizações futuras do template. Sem mensalidade. Sem custos ocultos.

### Qual é a política de reembolso?

Como este é um produto digital com acesso imediato ao código-fonte após a compra, reembolsos não são oferecidos como política geral. Se você encontrar problemas técnicos após a compra, entre em contato primeiro com [hello@dankoai.com](mailto:hello@dankoai.com) — faremos o possível para ajudar a resolver.

-----

## Sobre a tecnologia

### Quanto conhecimento técnico preciso para usar o vibefast.app template?

O suficiente para ler e seguir os tutoriais deste repositório. O vibefast.app template foi projetado para ser amigável para vibe coders — combinado com ferramentas de IA como Cursor ou Claude, pessoas sem experiência profunda em engenharia conseguem colocá-lo para funcionar. Se você consegue executar `npm install` e entender o que o código gerado por IA está fazendo, é o suficiente.

### Preciso de uma conta paga do Cloudflare?

Não. O plano gratuito do Cloudflare é mais do que suficiente para lançar um app real — Workers com 100K requisições/dia, D1 com 100K leituras/dia, R2 com 1M operações/mês. Você pode fazer upgrade quando seu app realmente tiver o tráfego que justifique.

### O vibefast.app template vai suportar outros stacks no futuro?

O vibefast.app template é focado no full-stack do Cloudflare (Remix + Workers + D1 + R2) — esse é o posicionamento central. Suportar outros stacks ou plataformas de deploy não está no roadmap atual.

### Posso usar o vibefast.app template em múltiplos projetos?

Sim. A licença atual suporta uso em múltiplos projetos. Você pode usar livremente em quantos projetos pessoais ou de clientes quiser — incluindo trabalho de agência.

### Qual a diferença do vibefast.app template para outros boilerplates como ShipFast ou Supastarter?

O vibefast.app template é construído nativamente sobre o full-stack do Cloudflare (Remix + Workers + D1 + R2). As diferenças-chave: deploy real com um único comando (`npm run setup`), zero cold starts no edge, sem hosting de banco de dados separado, e Service Binding entre frontend e API com zero overhead de CORS. Não é um template genérico — é uma arquitetura com opinião, testada em produção.

-----

## Sobre o suporte

### Quem eu contato se tiver dúvidas após a compra?

Você pode nos contatar por:

- Email: [hello@dankoai.com](mailto:hello@dankoai.com)
- X: [@dankopeng](https://x.com/dankopeng)

### Com que frequência o vibefast.app template é atualizado?

O vibefast.app template segue um modelo de melhoria contínua. Todas as atualizações são publicadas no repositório privado e estão incluídas na sua compra sem custo adicional.

### Quero entender Vibe Coding antes de comprar. Por onde começo?

Comece aqui:

- [O que é Vibe Coding?](./pt-br/01-what-is-vibecoding-pt-br.md) — conceitos-chave e terminologia
- [Por que Cloudflare é a melhor escolha para Vibe Coding?](./pt-br/05-the-best-way-to-vibecoding-on-cloudflare-pt-br.md) — introdução prática

### Acabei de comprar. Qual o primeiro passo?

Siga o [Guia de Início Rápido](./quickstart-pt-br.md) — você vai do clone a um app no ar em menos de 10 minutos.

-----

Dúvidas gerais? Abra uma issue no [GitHub](https://github.com/vibefast-app/vibefast-docs/issues).  
Problemas técnicos após a compra? Abra uma issue no repositório privado ou envie um email para [hello@dankoai.com](mailto:hello@dankoai.com).  
Ou entre em contato diretamente com [@dankopeng](https://x.com/dankopeng).
