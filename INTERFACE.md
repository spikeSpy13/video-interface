# Interface local para vídeo

Abra uma página no seu Mac para escrever prompts, verificar sua descrição, gerar vídeos pelo OpenRouter, assistir e baixar o MP4. O arquivo `app.py` contém a página e o servidor; não importa outros arquivos deste projeto.

## Abrir no Mac

Depois de clonar este repositório para `~/video-interface`, execute no Terminal:

```bash
cd "$HOME/video-interface"
python3 app.py --ask-key --open --port 0
```

O programa pede a chave OpenRouter de forma oculta e abre seu navegador. A chave fica apenas na memória do processo local e não é salva em arquivo nem enviada à página. Se a chave já estiver configurada em `OPENROUTER_API_KEY`, o programa usa essa configuração. Aperte Enter sem informar uma chave para apenas visualizar a página; os botões de API ficam indisponíveis.

Mantenha o Terminal aberto. Para encerrar, use Ctrl+C. `--port 0` escolhe uma porta disponível. Para abrir sem lançar o navegador automaticamente, omita `--open` e use o endereço mostrado pelo Terminal no navegador desse mesmo computador.

O projeto requer Python 3.12 ou mais recente, sem instalação de dependências. No Mac com Python 3.14 instalado, usa automaticamente o pacote `certifi` se ele estiver disponível e se `SSL_CERT_FILE` não estiver configurado. A verificação TLS permanece ativa. Se seu Python ainda apresentar erro de certificado, configure no mesmo Terminal:

```bash
export SSL_CERT_FILE="$(python3 -c 'import certifi; print(certifi.where())')"
```

## Usar a página

O catálogo do OpenRouter define as durações, resoluções, formatos e suporte a áudio. O modelo inicial é `google/veo-3.1-lite`, quando disponível, com o máximo de duração anunciado pelo modelo, 1080p se disponível, vertical 9:16 e sem áudio. Você pode trocar os parâmetros e modelos. A estimativa de vídeo só aparece quando há um SKU por segundo conhecido para a combinação; não inclui o custo de verificação. Os valores efetivos são os retornados pelo provedor.

**Verificar prompt** faz uma chamada de texto ao modelo `openai/gpt-oss-safeguard-20b`; pode consumir uma pequena parte do saldo, mas não cria vídeo. O resultado se refere à descrição verificada. Alterar o texto limpa esse resultado.

**Gerar vídeo** verifica novamente a descrição no servidor. A resposta precisa ser um JSON completo e consistente que aprove o pedido. Uma recusa, falta de credencial, falha de rede ou resposta inválida interrompe o fluxo antes do POST de vídeo. Quando aprovado, o servidor envia uma única geração paga, consulta o status e baixa o MP4. A página mostra a prévia, o custo informado pelo OpenRouter e o botão de download.

O verificador permite romance e intimidade não explícita entre adultos, nudez artística não sexual de adultos, cenas infantis não sexualizadas, ação, terror e violência fictícia com efeitos de cinema. Estilo realista, sangue ou menção a um tema não bastam para bloquear uma cena fictícia permitida.

Continuam recusados sexualização de menores/personagens com aparência infantil, pornografia e atos sexuais explícitos, violência sexual/sexualização sem consentimento, violência extrema envolvendo vítimas ou acontecimentos reais e instruções de dano. Idade ou consentimento incertos em contexto sexual impedem aprovação. A avaliação automática tem limites e não garante detectar todos os pedidos; as regras do provedor continuam aplicáveis.

## Histórico e retomada

Os MP4s ficam em `outputs/`, ao lado de `app.py`. IDs e status ficam em `.runtime/studio-jobs/`, sem chave ou prompt. Fechar a aba não interrompe o servidor.

Se um trabalho com ID confirmado sofrer interrupção, clique em **Retomar**. Isso consulta o trabalho existente e não envia um novo POST de geração. Se o envio inicial não tiver sido confirmado, confira os trabalhos na conta OpenRouter antes de gerar de novo. A prevenção de duplicidade vale para o identificador de envio; recarregar a página e enviar um novo pedido pode criar outra cobrança.

## Validação realizada

38 testes HTTP com provedor simulado passaram: verificação antes do vídeo, recusa, falha de rede, JSON inválido, consistência e completude do veredito, interrupção antes do envio, parâmetros do catálogo, proteção de origem, envio único, retomada, download por faixas, diagnóstico de erros HTTP e ocultação de dados sensíveis.

HTTP 400 em uma criação de vídeo significa pedido rejeitado: a página mostra a mensagem estruturada do provedor quando disponível. Falhas de rede continuam identificadas como envios possivelmente interrompidos, sem repetição automática. A mensagem detalhada de um erro antigo descartada por uma versão anterior não pode ser recuperada do histórico local.

O fluxo também passou no Chromium com um MP4 real de teste: verificação sem criar vídeo, bloqueio, descarte de respostas antigas após editar o texto, recuperação de resposta interrompida sem duplicar geração, reprodução, download idêntico, resolução máxima do catálogo, recuperação de falha do catálogo e layout em 320 pixels sem rolagem horizontal. Nenhuma exceção JavaScript ocorreu.

O catálogo público real foi consultado e confirmou Veo Lite com até 8 segundos e 1080p, além da disponibilidade do modelo de verificação. Esses dados não comprovam a disponibilidade para uma conta específica. Nenhuma geração paga ou chamada real autenticada de verificação foi executada: a chave não está configurada neste ambiente na nuvem. Para usar no Mac, forneça-a apenas no Terminal local.
