# Runbook — publicação de APK no GitHub Releases

**Projeto:** ComprovaPro · **Responsável:** InovaSys Solutions · **Revisão:** 22/11/2026  
**Tipo de acesso:** o repositório `afprado/comprovapro-android-releases` é **público**. O código-fonte e a chave de assinatura são **privados** em outros locais.

> **Estado atual (22/11/2026):** a release `android-v1.0.0-build15` está **PUBLICADA**. A API do GitHub confirma o asset e o SHA-256 do upload; **download sem sessão GitHub pelo botão da Cloudflare Pages confirmado; hash e tamanho do APK baixado conferidos no PowerShell: PASS em 22/11/2026**. Este runbook permanece válido para próximas versões.

## 1. Separação obrigatória de responsabilidades

| Componente | Local | Regras |
|---|---|---|
| Código Android e configuração de build | Repositório privado `afprado/comprovapro-app` | Nunca copiar o código para este repositório |
| Assinatura do app | Cofre privado / máquina de build autorizada | Nunca disponibilizar `*.jks`, `*.keystore`, senhas e certificados com chave privada |
| APK aprovado | Artefato binário da versão validada | Publicar **como asset de uma GitHub Release**, não como commit na árvore `main` |
| Metadados e instruções | README, `docs/`, `SECURITY.md` deste repositório | Podem ser públicos; não incluir dados pessoais ou segredos |
| Página oficial para clientes | `comprovapro.app.br/baixar/` | Ativar somente com APK público e SHA-256 conferidos |

## 2. Identidade do build 15 aprovado

| Campo | Valor esperado |
|---|---|
| Produto | ComprovaPro |
| Package ID | `com.inovasys.comprovapro` |
| Versão Android (versionName) | `1.0.0` |
| Código da versão (versionCode) | `15` |
| Nome do arquivo a publicar | `ComprovaPro-1.0.0-versionCode15-release.apk` |
| Tamanho | `93.911.115` bytes |
| SHA-256 | `d9caad800197189cdf8b286a3c43ed614bb88e10b1ebf17222bc45fa842d847b` |
| SHA-256 do certificado de assinatura | `1579f386d67fdd8865629c8bd8f28b34a78064599c694b8fb915ef753248f709` |
| Tag de distribuição proposta | `android-v1.0.0-build15` |

**Origem:** artefato compilado e validado em dispositivo real em 22/11/2026, com a atualização do ícone oficial, login e dados anteriores preservados. Os valores são a referência de validação **anterior ao upload**. **Conferir novamente** os bytes após baixar o arquivo público antes de usá-lo como fonte oficial.

## 3. Pré-publicação (sem efeitos no app, banco ou pagamentos)

- [ ] Confirmar que a conta GitHub possui acesso legítimo a este repositório e 2FA.
- [ ] Confirmar que o **arquivo exato** do APK15 aprovado está disponível na máquina do responsável.
- [ ] Confirmar que o binário **não é build HML**: deve possuir package `com.inovasys.comprovapro`.
- [ ] Conferir tamanho e SHA-256 **antes do upload** (abaixo).
- [ ] Opcional: verificar package, `versionCode`, `versionName` e certificado Android usando ferramentas `apkanalyzer`/`apksigner` oficiais instaladas.
- [ ] Garantir que nenhuma senha, keystore, `local.properties`, `.env`, backup ou arquivo privado acompanhe o envio.
- [ ] Consultar a [nota técnica da versão](BUILD15.md); não prometer recursos não homologados.

### Conferência no Windows PowerShell

```powershell
$apk = "C:\CAMINHO\AUTORIZADO\ComprovaPro-1.0.0-versionCode15-release.apk"
if (!(Test-Path -LiteralPath $apk)) { throw "APK não encontrado. Interrompa." }

$expectedBytes = 93911115
$expectedSha256 = "d9caad800197189cdf8b286a3c43ed614bb88e10b1ebf17222bc45fa842d847b"

$actualBytes = (Get-Item -LiteralPath $apk).Length
$actualSha256 = (Get-FileHash -LiteralPath $apk -Algorithm SHA256).Hash.ToLowerInvariant()

if ($actualBytes -ne $expectedBytes -or $actualSha256 -ne $expectedSha256) {
  throw "FAIL: APK diferente do build aprovado. NÃO PUBLIQUE."
}
"PASS: integridade aprovada — $actualBytes bytes — $actualSha256"
```

### Conferência em macOS / Linux

```sh
shasum -a 256 ComprovaPro-1.0.0-versionCode15-release.apk
wc -c < ComprovaPro-1.0.0-versionCode15-release.apk
```

Compare ambos os resultados com o quadro acima. **Não basta olhar o nome do arquivo.**

## 4. Criar release em rascunho (GitHub Web)

1. Abra **Releases → Draft a new release** no repositório público de distribuição.
2. Crie a nova tag `android-v1.0.0-build15` a partir da `main` (ou escolha a tag já existente, se for exatamente a correta). **Não usar uma tag já publicada para outro APK.**
3. **Release title:** `ComprovaPro Android 1.0.0 (build 15)`.
4. Copie a descrição revisada de [`docs/BUILD15.md`](BUILD15.md), sem declarações de Play Store ou outros canais não homologados.
5. Arraste **somente** `ComprovaPro-1.0.0-versionCode15-release.apk` para **Attach binaries by dropping them here or selecting them**, na seção dos **assets da Release**. **Não use "Add file" na página Code.**
6. Salve como **Draft**. Antes de publicar, verifique nome, tamanho, SHA-256 de origem, política de integridade da plataforma e ausência de arquivos extras.
7. **Somente mediante aprovação de distribuição pública**, clique em Publish release; não marque "prerelease" se esta for a versão estável autorizada.
8. Guarde URL da release, tag, data/hora e captura da lista de assets; o link oficial esperado terá a forma:  
   `https://github.com/afprado/comprovapro-android-releases/releases/download/android-v1.0.0-build15/ComprovaPro-1.0.0-versionCode15-release.apk`  
   **Essa URL é apenas um FORMATO PREVISTO; não presumir que já existe.**

## 5. Verificação após upload/publicação

- [ ] Abrir a página de release de um navegador sem sessão GitHub e confirmar se está publicamente disponível.
- [ ] Baixar o APK pelo link direto **de fato publicado**, sem redirecionamento para autenticação.
- [ ] Executar novamente a conferência de **93.911.115 bytes e SHA-256** no arquivo **baixado**.
- [ ] Se necessário, conferir também package e assinatura do binário baixado. Hash idêntico confirma que são os mesmos bytes do APK aprovado.
- [ ] Registrar resultado **PASS/FAIL** e URL real em registro de publicação; nenhuma evidência deve conter dados privados.
- [ ] Após PASS, preencher a configuração da landing `baixar/config.js` com a URL pública, `versionCode: 15`, tamanho, hash e data de **publicação efetiva**.
- [ ] Alterar a navegação do CTA em `index.html` somente quando a URL do APK, a página e o destino oficial estiverem liberados.
- [ ] Validar a homepage e `/baixar/` no Cloudflare Pages `pages.dev` antes de migrar DNS ou domínio oficial.
- [ ] Preservar a marca, o Worker de aprovação, o Admin, Supabase e Mercado Pago. A abertura comercial de pagamentos é uma etapa **independente**, sujeita à homologação financeira e de infraestrutura.

## 6. Política para futuras versões

- **Nova alteração de binário exige novo `versionCode` e nova tag**. Não substituir silenciosamente o asset de uma release já anunciada.
- Manter o identificador `com.inovasys.comprovapro` e a **mesma assinatura de produção** para permitir atualização sem desinstalação.
- Registrar data, status, Git SHA do código privado (em registro interno), hash do APK, certificado, plano de rollback, testes reais e aprovação.
- Verificar as opções atuais de imutabilidade da Release no GitHub: elas dependem de configurações e disponibilidade do repositório; **não presumir ativação**.
- Não usar GitHub Releases como mecanismo de autoatualização sem uma implementação própria, assinada e auditada.
- Nunca instruir o cliente a desativar o Google Play Protect. Se houver bloqueio ou alerta de segurança, interromper e contatar o suporte.

## 7. Critérios de aceitação

**PASS** somente se a release pública possui o APK de produção **exatamente idêntico ao binário validado**, sem arquivos confidenciais, e o download externo é bem-sucedido.

**FAIL/NO-GO** se faltar o APK, se o hash/tamanho divergir, se o asset apontar a HML, se o navegador exigir autenticação, se a release contiver segredos ou se não houver autorização para liberação pública.

---

Referência: [GitHub Releases — documentação oficial](https://docs.github.com/en/repositories/releasing-projects-on-github/about-releases).