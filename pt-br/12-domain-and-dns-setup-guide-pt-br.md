# O Que É Domínio e DNS? O Que Fazer Depois de Comprar um Domínio

**Autor:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Atualizado:** Março de 2026  
**Tempo de leitura:** ~7 minutos

-----

## Você Comprou um Domínio. E Agora?

Muitas pessoas compram um domínio e não sabem o que fazer depois.

O domínio está pronto, o app está implantado, mas os dois não estão conectados — acessar o domínio mostra uma página em branco ou "site não encontrado."

Este tutorial explica a relação entre domínios e DNS, e percorre exatamente como apontar seu domínio para o Cloudflare Workers.

-----

## O Que É um Domínio?

Um **domínio** é o endereço do seu app — algo como `vibefast.app`.

Seu app na verdade roda em um endereço IP, como `104.21.45.123`. Mas ninguém quer memorizar números, então os domínios existem — um nome memorável que mapeia para um endereço numérico.

Domínios são comprados de registradores de domínio (Namecheap, GoDaddy e Cloudflare Registrar todos funcionam). `.app`, `.com`, `.io` são diferentes domínios de nível superior (TLDs) com preços e casos de uso ligeiramente diferentes.

-----

## O Que É DNS?

**DNS (Domain Name System)** é o sistema que traduz nomes de domínio em endereços IP.

Pense nele como uma lista telefônica:

```
vibefast.app → 104.21.45.123
google.com → 142.250.80.46
```

Quando alguém digita `vibefast.app` em um navegador, aqui está o que acontece:

```
1. Navegador pergunta ao DNS: qual é o endereço IP de vibefast.app?
2. DNS pesquisa e responde: 104.21.45.123
3. Navegador se conecta a esse IP e carrega o site
```

Todo o processo acontece em milissegundos — você nunca percebe.

-----

## Tipos de Registros DNS

Ao configurar DNS, você verá vários tipos de registro:

|Tipo de Registro|Propósito                         |Exemplo                                              |
|----------------|----------------------------------|-----------------------------------------------------|
|`A`             |Aponta domínio para endereço IP   |`vibefast.app → 104.21.45.123`                       |
|`CNAME`         |Aponta domínio para outro domínio |`www.vibefast.app → vibefast.app`                    |
|`MX`            |Configuração do servidor de e-mail|Roteia e-mail para `@vibefast.app`                   |
|`TXT`           |Fins de verificação               |Prova propriedade do domínio para Google, Stripe, etc.|

-----

## Conectando Seu Domínio ao Cloudflare Workers

A configuração tem duas partes: **primeiro, mover o gerenciamento de DNS do seu domínio para o Cloudflare; segundo, vincular seu domínio personalizado dentro do Workers.**

### Passo 1: Mover Gerenciamento de DNS para o Cloudflare (Recomendado)

Se você comprou seu domínio pelo Cloudflare Registrar, o DNS já é gerenciado pelo Cloudflare — pule para o Passo 2.

Se seu domínio está registrado em outro lugar (Namecheap, GoDaddy, etc.):

1. Faça login no [Cloudflare Dashboard](https://dash.cloudflare.com) e clique em "Add a domain"
1. Digite o nome do seu domínio e selecione o plano gratuito
1. O Cloudflare automaticamente varre e importa seus registros DNS existentes — verifique se nada está faltando
1. O Cloudflare fornece dois endereços de Nameserver, por exemplo:
   
   ```
   aria.ns.cloudflare.com
   bob.ns.cloudflare.com
   ```
1. Vá ao seu registrador de domínio e substitua os Nameservers atuais por esses dois
1. Aguarde a propagação — geralmente alguns minutos a algumas horas, máximo 48 horas

Uma vez que entre em vigor, todas as suas configurações de DNS ficam dentro do Cloudflare ao lado do seus Workers, D1 e R2.

### Passo 2: Definir Domínio Personalizado no Workers

Após o DNS ser gerenciado pelo Cloudflare, entre nas configurações do seu Worker para vincular o domínio:

1. Faça login no [Cloudflare Dashboard](https://dash.cloudflare.com)
1. Clique em "Workers & Pages" na barra lateral esquerda
1. Clique no seu **Worker de frontend** (no VibeFast, esse é o worker web, não o worker de API)
1. Vá em "Settings" → "Domains & Routes"
1. Clique em "Add" → "Custom Domain"
1. Selecione seu domínio no menu suspenso — ele vai listar domínios já registrados na sua conta Cloudflare
1. Clique em "Add Custom Domain"

Nota: seu domínio deve já estar na sua conta Cloudflare (ou seja, o Passo 1 está completo) antes de aparecer no menu suspenso.

O Cloudflare automaticamente:

- Adiciona o registro DNS correspondente
- Solicita e configura um certificado SSL
- Roteia o tráfego desse domínio para o seu Worker

Alguns minutos depois, abra um navegador, digite seu domínio e seu app carrega.

-----

## Como Verificar a Configuração

```bash
# Verificar registros DNS do seu domínio
nslookup vibefast.app

# Ou use dig
dig vibefast.app

# Confirmar certificado HTTPS
curl -I https://vibefast.app
```

Se você ver o IP correto ou CNAME na saída, o DNS propagou com sucesso.

-----

## Perguntas Comuns

**P: Quanto tempo leva a propagação do DNS?**

Em teoria até 48 horas, mas na prática geralmente alguns minutos a algumas horas. Se passaram mais de 2 horas sem mudança, verifique se todos os registros foram preenchidos corretamente.

**P: Preciso configurar `www.seudominio.com` e `seudominio.com` separadamente?**

Sim. Você precisa configurar cada um separadamente:

- `seudominio.com` (o domínio apex/raiz)
- `www.seudominio.com` (o subdomínio www)

Normalmente você apontaria ambos para o Workers, ou apontaria `www` para o domínio apex com um CNAME e configuraria um redirecionamento para que os usuários sempre cheguem em uma versão canônica.

**P: Preciso obter um certificado SSL eu mesmo?**

Não. Uma vez que seu domínio aponta para o Cloudflare, certificados SSL são automaticamente provisionados e renovados. Seu app automaticamente suporta HTTPS — nada a configurar.

-----

## Resumo

Um domínio é o endereço do seu app. DNS é o sistema que traduz esse endereço em um IP. O fluxo de configuração:

1. Compre um domínio
1. Mova o gerenciamento de DNS para o Cloudflare (recomendado)
1. Vincule o domínio personalizado no Cloudflare Workers → "Settings" → "Domains & Routes"
1. O Cloudflare cuida do HTTPS automaticamente

O processo todo geralmente leva menos de 30 minutos.

-----

👉 [Próximo: Cloudflare Workers vs Servidores Tradicionais](./06-cloudflare-workers-vs-traditional-server-pt-br.md)

Dúvidas? Me encontre no X em [@dankopeng](https://x.com/dankopeng).  
👉 **[vibefast.app](https://vibefast.app)** — Early bird $99, preço sobe para $199 em 1º de junho de 2026.
