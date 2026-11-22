# ComprovaPro — Distribuição oficial Android

**InovaSys Solutions** · **Faça. Registre. Comprove.**

Repositório **público e exclusivo para a distribuição dos instaladores Android oficiais** do ComprovaPro. Aqui ficam as informações de versão, integridade, segurança e procedimentos de publicação para profissionais de desenvolvimento e suporte.

> **Estado em 22/11/2026:** repositório criado e documentação técnica preparada; **nenhum APK publicado ainda**. A versão Android **1.0.0 / build 15** foi aprovada em aparelho real, mas a release pública depende do upload e da conferência de integridade. **Não confunda o APK aprovado com um arquivo disponível para download público.**

[**Consultar Releases**](https://github.com/afprado/comprovapro-android-releases/releases) · [**Site ComprovaPro**](https://comprovapro.app.br) · [**Segurança**](SECURITY.md) · [**Guia de publicação**](docs/RELEASE_CHECKLIST.md)

## 1. O que é o ComprovaPro

O ComprovaPro é um aplicativo Android da InovaSys Solutions desenvolvido para profissionais e prestadores de serviço organizarem e comprovarem seus atendimentos.

**Fluxo principal de negócio:**

~~~mermaid
flowchart LR
    A["Proposta"] --> B["Aprovação"]
    B --> C["Execução"]
    C --> D["Materiais e fotos"]
    D --> E["Assinatura"]
    E --> F["Conclusão"]
    F --> G["Pagamento registrado"]
    G --> H["Comprovante"]
~~~

O código-fonte, o painel administrativo e as integrações de pagamento são mantidos **separadamente, em repositórios privados**. Este repositório não inclui banco de dados, backend ou segredos.

## 2. Onde baixar o aplicativo

| Canal | Situação em 22/11/2026 | Orientação |
|---|---|---|
| [GitHub Releases](https://github.com/afprado/comprovapro-android-releases/releases) | **Aguardando primeira publicação** | Após autorização, o instalador aparecerá como **asset da Release**, acompanhado dos dados de integridade |
| [Site oficial](https://comprovapro.app.br) | Página comercial existente; distribuição Android em preparação | Acessar a página oficial quando o lançamento for anunciado |
| Google Play | Publicação não confirmada nesta data | Não instalar aplicativos semelhantes presumindo serem oficiais |

**Não existe APK para baixar na árvore de arquivos (aba `Code`) deste repositório.** O instalador será distribuído como anexo de uma **Release**, nunca como commit comum ou arquivo enviado por terceiros.

## 3. Versão aprovada para distribuição

| Propriedade | Valor |
|---|---|
| Aplicativo | **ComprovaPro** |
| Canal | Android · produção |
| Versão | **1.0.0** |
| Código interno | **15** |
| Identificador Android | `com.inovasys.comprovapro` |
| Arquivo aprovado | `ComprovaPro-1.0.0-versionCode15-release.apk` |
| Tamanho do APK | **93.911.115 bytes** (aproximadamente 94 MB) |
| SHA-256 do APK | `d9caad800197189cdf8b286a3c43ed614bb88e10b1ebf17222bc45fa842d847b` |
| Situação | **Homologado no Android em 22/11; publicação pública pendente** |

**Validação realizada:** atualização do aplicativo em aparelho real, novo ícone e logotipo oficiais, login e preservação dos serviços existentes. Isso não é garantia de funcionamento em todos os modelos de Android.

A ficha detalhada, incluindo o certificado de assinatura, está em [**Build 15 — informações verificáveis**](docs/BUILD15.md).

## 4. Instalação e atualização segura

Quando a release oficial estiver publicada:

1. Acesse o link de download divulgado no **site oficial** do ComprovaPro.
2. Baixe o APK correspondente à versão anunciada e confira a procedência.
3. Se já utiliza o aplicativo, **atualize sem desinstalar** para preservar a conta e os dados locais.
4. Caso o Android solicite permissão para instalar um APK obtido pelo navegador, conceda-a somente se confiar na origem e no arquivo; depois é possível revogar a permissão.
5. **Não desative o Google Play Protect.** Se a instalação for bloqueada ou surgir alerta de segurança, interrompa e procure o suporte.

Administradores técnicos podem comparar o **SHA-256 do APK baixado** ao valor da release. O arquivo público deve ser idêntico ao binário aprovado. Em caso de diferença, **não instalar nem distribuir**.

## 5. Arquitetura de distribuição

~~~mermaid
flowchart TB
    SRC["Desenvolvimento Android (GitHub privado)"] --> VALID["Compilação assinada + validação do APK"]
    VALID --> CHECK{"SHA-256, tamanho e assinatura conferidos?"}
    CHECK -- "Não" --> STOP["Bloquear distribuição"]
    CHECK -- "Sim" --> DRAFT["GitHub Release — rascunho"]
    DRAFT --> APPROVE{"Revisão e autorização"}
    APPROVE -- "Não" --> STOP
    APPROVE -- "Sim" --> PUB["Release pública com APK verificado"]
    PUB --> SITE["Página oficial de download — Cloudflare Pages"]
    SITE --> USER["Usuário Android"]
~~~

**Separação de responsabilidades:** o GitHub Releases armazena a versão autorizada; a Cloudflare Pages hospeda a página de apresentação; os serviços de dados e cobrança operam em infraestrutura própria. **Publicar o APK não ativa pagamentos nem muda o domínio comercial.**

## 6. Estrutura deste repositório

~~~text
comprovapro-android-releases/
├── README.md                  # Visão geral e acesso para técnicos e clientes
├── SECURITY.md                # Procedência, integridade e comunicação de incidentes
├── CHANGELOG.md               # Histórico de distribuição (publicação ≠ compilação)
├── .gitignore                 # Proteção contra versionamento de binários e segredos
└── docs/
    ├── BUILD15.md            # Versão 1.0.0 / build 15, hash, assinatura e notas da release
    └── RELEASE_CHECKLIST.md  # Processo de publicação, verificação e rollback
~~~

Os APKs **não aparecem nessa árvore**, porque serão anexados exclusivamente às releases.

## 7. Orientações para desenvolvedores e consultores

| Etapa | Controle obrigatório | Documento |
|---|---|---|
| Receber o binário | Verificar package, `versionCode`, tamanho e certificado | [Build 15](docs/BUILD15.md) |
| Validar integridade | Calcular SHA-256 no arquivo original **e novamente no arquivo baixado** | [Checklist de release](docs/RELEASE_CHECKLIST.md) |
| Publicar versão | Criar tag única, release em rascunho, revisão, publicação autorizada | [Checklist de release](docs/RELEASE_CHECKLIST.md) |
| Divulgar aos clientes | Somente depois da publicação, atualizar link e metadados no site e conferir acesso sem login | [Checklist de release](docs/RELEASE_CHECKLIST.md) |
| Responder a incidentes | Não substituir silenciosamente APK publicado; avaliar retirada e nova versão | [Segurança](SECURITY.md) |

**Padrão de versionamento:** a versão visível (`versionName`) e o número de compilação (`versionCode`) são diferentes. Toda mudança de APK precisa ser rastreável por artefato, assinatura, hash, data e tag. Não versionar chaves privadas, tokens, backups, APKs ou AABs diretamente nos commits.

**Governança:** branches e PRs para documentação; revisão e evidências PASS/FAIL para publicar; conservar versões ou registrar formalmente qualquer retirada.

## 8. Suporte e direitos

- **Suporte:** [suporte@inovasyssolutions.com.br](mailto:suporte@inovasyssolutions.com.br).
- **Segurança ou APK suspeito:** ver [SECURITY.md](SECURITY.md); não exponha dados confidenciais em issues públicas.
- **Direitos autorais:** © 2026 InovaSys Solutions. Todos os direitos reservados. Este repositório público **não torna o código proprietário open source**.
- **Limite operacional:** a distribuição de um instalador e a contratação de planos são processos distintos; esta documentação não certifica checkout público habilitado.

---

**ComprovaPro · InovaSys Solutions · Registro de distribuição iniciado em 22/11/2026.**