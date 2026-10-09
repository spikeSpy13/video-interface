# Interface de vídeo OpenRouter

Página local para escrever um prompt, consultar modelos e limites, verificar a descrição, gerar, assistir e baixar vídeos. `app.py` contém a interface HTML e o servidor Python. Requer Python 3.12 ou mais recente, sem GPU ou dependências obrigatórias.

## Abrir no Mac

Depois de clonar este repositório para `~/video-interface`, execute:

```bash
cd "$HOME/video-interface"
python3 app.py --ask-key --open --port 0
```

A chave OpenRouter é solicitada de forma oculta no Terminal e permanece na memória do servidor. Não é salva em arquivo ou enviada ao HTML. Se `OPENROUTER_API_KEY` já estiver configurada, essa variável é usada. A página abre no navegador do mesmo computador; mantenha o Terminal aberto durante o uso.

## Atualizar sem perder vídeos

Pare o servidor com Ctrl+C e execute:

```bash
cd "$HOME/video-interface"
git pull --ff-only
python3 app.py --ask-key --open --port 0
```

Vídeos em `outputs/` e histórico em `.runtime/` não são versionados e permanecem na pasta durante a atualização. Se o Git indicar mudanças locais ou históricos divergentes, não use `reset --hard` nem descarte arquivos; revise as alterações antes de atualizar.

O app anterior instalado pelo comando do chat fica em uma pasta `~/video-interface.XXXXXX`. Este clone não apaga essa instalação. Para trazer o histórico antigo, pare os dois servidores e copie suas pastas `outputs/` e `.runtime/` para o clone, preservando arquivos existentes. A chave não precisa ser migrada: informe-a novamente no Terminal.

## Trocar a chave

Pare o servidor com Ctrl+C. No mesmo Terminal, execute:

```bash
unset OPENROUTER_API_KEY
python3 app.py --ask-key --open --port 0
```

## Uso e custos

O catálogo define os parâmetros suportados. O modelo inicial é Veo Lite quando disponível, com duração máxima do modelo, 1080p quando disponível, vertical 9:16 e sem áudio. Outros modelos podem oferecer limites diferentes. A estimativa só aparece quando o catálogo tem um preço por segundo conhecido para a combinação.

HeyGen Video 1 sempre inclui áudio. Ao escolhê-lo, a página mostra **Com áudio (obrigatório)** e o servidor envia `generate_audio: true`, inclusive para pedidos vindos de uma aba antiga. Modelos com áudio opcional continuam permitindo **Sem áudio** e **Com áudio**. Nos demais, a página mostra **Definido pelo modelo** e não envia uma configuração de áudio. O campo `generate_audio: false` do catálogo, sozinho, não garante uma saída silenciosa.

**Verificar prompt** usa uma chamada de texto ao OpenRouter e pode consumir créditos; não cria vídeo. **Gerar vídeo** repete a verificação no servidor e envia uma única geração paga se o pedido for aprovado. O custo final do vídeo é o informado pela API. Uma falha ou resposta inválida na verificação impede o envio de vídeo. O verificador não garante identificar todos os pedidos e as regras do provedor continuam aplicáveis.

A política permite romance e intimidade não explícita entre adultos, nudez artística não sexual de adultos e violência fictícia de cinema. Mantém restrições para sexualização de menores, sexo explícito, violência sexual, violência extrema envolvendo vítimas ou eventos reais e instruções de dano. Não altera filtros do HeyGen ou dos provedores OpenRouter.

Se a geração com ID confirmado for interrompida, use **Retomar**. Isso consulta o trabalho existente sem enviar outro POST de geração. Um envio não confirmado exige consultar sua conta OpenRouter antes de gerar novamente. Recarregar a página e enviar outro pedido pode criar outra cobrança.

Um HTTP 400 no POST de vídeo aparece como **Pedido rejeitado pela API**, com a mensagem estruturada do OpenRouter/provedor quando disponível. Corrija o motivo informado antes de enviar outro pedido. A chave e o prompt completo são ocultados na mensagem; respostas brutas de diagnóstico não são exibidas. Falhas de rede e erros de servidor continuam tratados como envios possivelmente interrompidos, sem repetição automática.

## Certificados no Mac

O programa preserva a verificação TLS e usa `certifi` automaticamente no Mac se estiver disponível. Caso seu Python ainda tenha erro de certificado e `certifi` esteja instalado, configure no mesmo Terminal:

```bash
export SSL_CERT_FILE="$(python3 -c 'import certifi; print(certifi.where())')"
```

## Validação

49 testes HTTP com API simulada passaram, incluindo aprovação, bloqueio, resposta inválida, envio único, retomada, download, origem, diagnóstico do HTTP 400, ocultação de dados sensíveis e configuração de áudio obrigatório/opcional/definido pelo modelo. O navegador Chromium também validou reprodução, download, recuperação de resposta interrompida, opções de áudio, ausência de reenvio e layout de celular. O catálogo público real foi consultado. Nenhuma chamada real autenticada de verificação ou geração paga foi executada no ambiente de desenvolvimento: a chave não está configurada nele. A precisão do classificador e a qualidade dos vídeos não foram validadas.

Mais detalhes estão em [INTERFACE.md](INTERFACE.md).
