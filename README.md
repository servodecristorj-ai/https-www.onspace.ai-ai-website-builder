# Recon em uma plataforma de geração de apps por IA: algumas observações interessantes

Recentemente decidi fazer um exercício de reconhecimento passivo em uma plataforma de geração de aplicativos por IA.

O objetivo não era explorar nada nem procurar acesso indevido, mas simplesmente observar o que a própria aplicação revelava durante seu funcionamento normal.

## O experimento

Criei um aplicativo simples utilizando autenticação por código enviado por e-mail e uma área restrita para usuários autenticados.

Para entender melhor o comportamento da plataforma, utilizei um endereço de e-mail Canarytoken durante o fluxo de autenticação.

O resultado foi interessante.

## Infraestrutura de envio de e-mails

Ao acionar o envio do código de acesso, o Canarytoken registrou a entrega da mensagem.

A análise dos cabeçalhos revelou:

- utilização do domínio de autenticação da plataforma;
- assinaturas DKIM válidas;
- infraestrutura de entrega ligada aos serviços da Alibaba Cloud (Aliyun);
- hostnames e metadados operacionais do serviço de e-mail.

Nada disso representa uma vulnerabilidade por si só.

Porém, é um bom exemplo de como metadados expostos durante operações normais podem ajudar a compreender a arquitetura utilizada por uma aplicação.

## Exposição de contexto técnico pelo agente

O aspecto mais curioso apareceu durante o uso do agente de geração de código.

Em determinadas respostas, o sistema apresentou informações que pareciam fazer parte de seu contexto interno de implementação, incluindo referências a:

```text
@better-auth/core
memory-adapter.d.ts
MemoryDB
DBAdapter
MemoryAdapterConfig
```

Além disso, também foram exibidos nomes de arquivos que supostamente foram modificados durante a geração do projeto:

```text
services/emailAuthService.ts
hooks/useEmailAuth.ts
hooks/useMemberSession.ts
components/auth/...
app/login.tsx
app/index.tsx
app/_layout.tsx
```

Do ponto de vista de segurança, isso não significa acesso ao código-fonte da plataforma.

Entretanto, fornece sinais interessantes sobre:

- bibliotecas utilizadas;
- organização do projeto gerado;
- padrões de autenticação;
- estrutura de diretórios;
- abstrações utilizadas pelo mecanismo de geração.

## O que isso não significa

É importante destacar o que NÃO foi observado:

- nenhuma credencial;
- nenhuma chave de API;
- nenhum segredo operacional;
- nenhum banco de dados exposto;
- nenhum acesso administrativo;
- nenhum bypass de autenticação;
- nenhuma execução arbitrária de código.

Trata-se de uma observação de exposição de informações e metadados, não de comprometimento de segurança.

## O que achei mais interessante

O ponto mais interessante foi perceber como agentes de IA frequentemente revelam muito mais contexto interno do que um software tradicional.

Enquanto aplicações convencionais costumam devolver apenas o resultado final, sistemas orientados por IA frequentemente exibem:

- raciocínios intermediários;
- listas de arquivos;
- nomes de componentes;
- dependências;
- detalhes de implementação.

Individualmente essas informações podem parecer irrelevantes.

Mas, para quem trabalha com segurança, cada detalhe ajuda a montar um mapa mais completo da arquitetura observada.

## Conclusão

Este exercício reforçou uma lição clássica de reconhecimento:

Nem sempre o mais interessante está em uma vulnerabilidade evidente.

Muitas vezes o valor está na combinação de pequenos sinais que uma plataforma expõe naturalmente durante seu funcionamento.

Cabeçalhos de e-mail, nomes de serviços, bibliotecas utilizadas, estruturas de arquivos e comportamentos do agente podem não representar risco imediato, mas são informações que ajudam a entender como o sistema foi construído.

Antes de publicar este estudo, enviei as observações para a equipe responsável para que pudessem avaliar internamente se algum dos comportamentos descritos possui impacto de segurança não visível para um observador externo.