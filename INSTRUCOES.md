# Instruções para o Claude Code: publicar o app do Google

Você é o Claude Code rodando no vault de um estudante. Ele te mandou este link porque a conexão dele com o Google (Gmail, Drive, Agenda) está em modo **Testing**, e nesse modo a autorização vence a cada 7 dias. Seu trabalho é deixar o app **In production**, pra conexão ficar permanente.

O Google só publica o app se ele tiver página inicial, política de privacidade e domínio autorizado. Os dois HTML deste repositório são o modelo dessas páginas. Você vai criar um site **na conta do GitHub do próprio estudante** a partir deste modelo, e nunca no repositório do modelo.

Fale com o estudante em português, em frases curtas. Ele pode não ser técnico. Antes de cada parte, diga em uma linha o que vai fazer.

## Regras

- **Nunca** coloque `credentials.json`, `token*.json`, senha ou e-mail lido no repositório do site. O site só leva os dois HTML.
- **Não mande o app pra verificação do Google** e não envie nada pra revisão. Uso pessoal com menos de 100 usuários não precisa: https://support.google.com/cloud/answer/13464323
- **Não coloque logo no app.** Logo obriga a verificação.
- Se o Google pedir pra **provar que o domínio é seu** (Search Console, registro TXT), pare e peça pro estudante chamar o mentor.
- Se uma etapa no navegador travar duas vezes, pare de tentar e passe a ditar tela por tela pro estudante clicar.

## 0. Conferir se precisa

Descubra o projeto do Google Cloud que o vault usa (procure o `credentials.json` e o `project_id` dentro dele). Abra o console em `https://console.cloud.google.com/auth/audience?project=PROJECT_ID` e veja o *Publishing status*.

- Se já estiver **In production**, avise o estudante e pare.
- Se estiver **Testing**, siga.

## 1. Juntar os dados

Pergunte ao estudante, de uma vez:

1. **Nome** que deve aparecer no site (ex.: "Ana Souza").
2. **Nome do app** (sugira "Assistente de {primeiro nome}"). Não pode ter "Google" nem "Gmail".
3. **E-mail de contato** que vai aparecer no site. Avise que o site é público. Pode ser o mesmo da conta Google.
4. Se ele **já tem conta no GitHub**. Se não tiver, peça pra criar agora em https://github.com/signup com um usuário simples, sem espaço, e confirmar o código do e-mail. Essa parte é com ele, você não consegue fazer.

## 2. Criar o site no GitHub dele

1. Confira se o `gh` (GitHub CLI) está instalado com `gh --version`. Se não estiver:
   - Windows: `winget install --id GitHub.cli -e --accept-source-agreements --accept-package-agreements`, depois feche e abra o terminal.
   - Mac: `brew install gh`.
2. Rode `gh auth login` (GitHub.com → HTTPS → login pelo navegador). O estudante copia o código de 8 dígitos e autoriza no navegador. Confirme com `gh auth status` e guarde o usuário (`USUARIO`) em minúsculas.
3. Crie o site a partir do modelo:
   ```
   gh repo create USUARIO.github.io --public --template danishiota10-code/template-site-app-google --clone
   ```
   Se já existir um repositório `USUARIO.github.io`, não apague nem sobrescreva: pergunte ao estudante se pode adicionar os dois arquivos nele.
4. Nos dois HTML do clone, troque `{{NOME}}`, `{{NOME_DO_APP}}`, `{{EMAIL}}` e `{{DATA}}` (data de hoje, DD/MM/AAAA). Confira que não sobrou nenhum `{{`. Apague deste clone o `INSTRUCOES.md` e o `README.md`, que são do modelo.
5. `git add`, `git commit -m "Site do app"`, `git push`.
6. Ative o GitHub Pages se ainda não estiver ativo:
   ```
   gh api -X POST repos/USUARIO/USUARIO.github.io/pages -f "source[branch]=main" -f "source[path]=/"
   ```
   O erro 409 quer dizer que já estava ativo, e está tudo bem.
7. Espere 1 ou 2 minutos e confira que `https://USUARIO.github.io` e `https://USUARIO.github.io/privacidade.html` respondem 200 (`curl -sI`). Se der 404, espere mais e tente de novo, até uns 5 minutos.

## 3. Publicar o app no Google

Use a extensão do Chrome (Claude in Chrome), no perfil do Chrome da mesma conta Google do vault. Se a extensão não estiver disponível, dite os passos pro estudante.

1. Abra `https://console.cloud.google.com/auth/branding?project=PROJECT_ID` e preencha:
   - Nome do app: o escolhido no passo 1 (se já tiver um nome sem "Google"/"Gmail", mantenha)
   - E-mail de suporte: o da conta Google
   - Logo: vazio (se houver, remova)
   - Página inicial: `https://USUARIO.github.io`
   - Política de privacidade: `https://USUARIO.github.io/privacidade.html`
   - Termos de serviço: vazio
   - Domínios autorizados: `USUARIO.github.io`
   - E-mail do desenvolvedor: o da conta Google
   - Salvar
2. Abra `https://console.cloud.google.com/auth/audience?project=PROJECT_ID`, clique em **Publish app** e confirme.
3. Confira que o status mudou pra **In production**. Se o botão estiver desativado, leia o motivo que o console mostra e corrija o campo que falta.

## 4. Autorizar de novo

A autorização feita em Testing continua vencendo em 7 dias mesmo depois de publicar, então precisa ser feita de novo.

1. Ache o token salvo pelo vault (procure `token*.json` perto do `credentials.json` ou dos scripts do Google) e o script de autorização que o gerou.
2. Antes de apagar, avise o estudante. Depois apague o token e rode o script de autorização.
3. Avise o estudante antes de o navegador abrir: ele vai ver "O Google não verificou este app". Ele clica em **Avançado → Acessar (nome do app) (não seguro)**, marca todas as caixinhas e clica em **Continuar**. Esse aviso é o padrão do Google pra app pessoal não verificado.
4. Se tiver mais de uma conta Google no navegador, peça pra ele escolher a mesma conta de antes.

## 5. Testar e fechar

1. Liste os 3 e-mails mais recentes e mostre só remetente e assunto.
2. Confirme pro estudante, em uma mensagem curta:
   - site no ar (os dois links)
   - app **In production**
   - autorização refeita
   - teste do Gmail funcionando
3. Registre no daily de hoje do vault dele que o app do Google foi publicado, com os links do site.
