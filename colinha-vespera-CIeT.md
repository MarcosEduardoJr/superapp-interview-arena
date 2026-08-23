# 🛡️ Colinha de Véspera — CI&T [Job-30469] Sênior Android + Agentes de IA

> Revisão rápida (30 min antes). Foco no que cai e nas **pegadinhas**. O requisito nº1 é **IA Agêntica** — comece por lá.

---

## 1. IA Agêntica & Agentes de Dev  ⭐ requisito nº1
- **Agente × workflow × copiloto**: agente = o LLM dirige o loop e escolhe ferramentas; workflow = código orquestra passos fixos (previsível/barato/testável); copiloto = sugere, humano no comando. *Comece single-agent; vá multi só quando a tarefa realmente se divide.*
- **Agentic SDLC** (o que a vaga pede): agente age da spec ao merge, com **gate automatizado em cada passo** (testes, lint, quality gate, review). **A máquina valida a máquina.**
- **Confiabilidade** = **evals + gates de CI + observabilidade**. ⚠️ Nunca responda "eu leio a saída dele".
- **Prompt injection** (risco nº1): dado de ferramenta é **dado, não instrução**. Defesa: menor privilégio, sandbox, validação de saída, human-in-the-loop em ações de efeito.
- **RAG** quando a base é grande/muda muito: embeddings + busca híbrida + **rerank** + **citar a fonte**. Reduz alucinação e custo vs. prompt gigante.
- **Custo**: contexto reenviado inteiro a cada iteração (super-linear) → **prompt caching** de prefixos estáveis, **model routing**, teto de iteração/budget.
- **HITL** é um dial: sugestão → semi-autônomo → autônomo. Ação **irreversível** exige aprovação + rollback.
- **MCP**: protocolo aberto que liga o agente a ferramentas/dados (o "USB-C", evita N×M).

## 2. SDD (Spec-Driven Development)
- Spec versionada = **fonte da verdade** (QUÊ/PORQUÊ), critérios de aceite verificáveis (Given/When/Then).
- ⚠️ Critério ruim: "deve ser rápido". Bom: "primeiro frame em até 1s no cold start".

---

## 3. Kotlin
- **StateFlow** = estado atual (tem valor inicial, p/ UI) · **SharedFlow** = eventos one-shot · **LiveData** = legado.
- ⚠️ `!!` desliga a null safety → NPE em runtime. Use `?.` `?:` `requireNotNull`.
- **structured concurrency**: escopo com Job cancela filhos (sem leak). ❌ `GlobalScope` = órfã/leak → use `viewModelScope`.
- **sealed** quando cada caso carrega dados (`Success(data)`) + `when` exaustivo · **enum** = constantes sem estado.

## 4. Coroutines & Flow
- **Frio** re-executa upstream por coletor · **hot** (StateFlow/SharedFlow) = multicast. Converta com `stateIn`/`shareIn`.
- Busca com resultado antigo (race) → `flatMapLatest` + `debounce` + `flowOn(IO)`.
- Testar: injetar dispatcher + `runTest` (tempo virtual, `advanceUntilIdle`) + **Turbine**. Sem `Thread.sleep`.
- `launch` = fire-and-forget (Job) · `async` = resultado (Deferred + await).

## 5. Android Core
- ⚠️ **ViewModel sobrevive a config change, NÃO a process death.** SavedStateHandle sobrevive a ambos (Bundle). Room/DataStore = durável.
- **MVI**: estado único imutável + fluxo unidirecional → previsível/testável (custo: boilerplate).
- **Migração XML→Compose**: incremental via interop (`ComposeView`/`AndroidView`), menor risco/maior tráfego primeiro, testes de caracterização. ❌ big-bang.
- Intent **explícito** (classe) × **implícito** (ação+dados).

## 6. Jetpack Compose
- 3 fases: **Composition → Layout → Drawing**. Recompõe só quem lê o estado que mudou.
- ⚠️ LazyColumn piscando/perdendo estado → falta `key = { it.id }` + modelo `@Immutable`.
- `remember` (recomposição) × `rememberSaveable` (rotação/process death).
- **State hoisting**: valor + callback → composable stateless, testável.

## 7. DI (Hilt/Dagger/Koin)
- Hilt/Dagger = **compile-time** (erro no build, sem reflexão) · Koin = runtime/DSL.
- ⚠️ Não dê `@Singleton` a tudo — singleton com Context de Activity = leak. Menor escopo que resolve.
- `@Binds` (interface→impl, eficiente) × `@Provides` (você constrói o objeto).

## 8. Navigation
- ⚠️ Passe **id, não o objeto** (Bundle limitado → `TransactionTooLarge`; acopla telas).
- `popUpTo(...){inclusive=true}` limpa a pilha (pós-login) · `launchSingleTop` não duplica.
- Bottom nav: `saveState`/`restoreState`, back stack por aba.

## 9. Persistência & Rede
- **Room** (relacional) × **DataStore** (chave-valor async) × **SharedPreferences** (legado, ANR). ⚠️ migrations ou crash/perda.
- **Offline-first**: Room é a fonte única; UI observa `Flow`; rede só atualiza cache.
- 401 no OkHttp → **Authenticator** renova uma vez (sincronizado).

## 10. Background
- **WorkManager** = garantido/deferível (sobrevive a reboot). **Foreground Service** = contínuo/visível agora (notificação). **AlarmManager** = horário exato.
- ⚠️ Android 14+: foreground service declara `foregroundServiceType`; `startForeground` em **< 5s** ou ANR.

## 11. Performance & R8
- Meça **antes** (Macrobenchmark/Profiler). Cold start: enxugar `Application.onCreate`, App Startup, **baseline profiles**.
- **R8** = shrink + obfuscate + optimize. ⚠️ Quebra reflexão/Gson sem regras `keep`; guarde `mapping.txt`.
- OOM: bitmaps sem downsampling, leaks (LeakCanary).

## 12. Build & Modularização
- ⚠️ `implementation` (encapsula, build incremental) × `api` (vaza transitivo). Padrão: `implementation`.
- Feature **não** depende de feature; compartilhado desce p/ `:core`.
- Rápido: configuration cache + build cache + paralelo + KSP (não KAPT). Version Catalog + convention plugins.

## 13. Segurança
- Token → EncryptedSharedPreferences/DataStore + chave no **Keystore**. TLS + pinning. ❌ hardcoded.
- Componentes: `exported=false` por padrão; valide Intents; `PendingIntent` `FLAG_IMMUTABLE`.
- ⚠️ Cert pinning quebra se o servidor rotaciona sem o app atualizar → pin de backup.

## 14. Release & Play
- **AAB** (a Play gera APKs por device) · **Play App Signing** guarda a chave; você mantém a **upload key**.
- ⚠️ `versionCode` sempre crescente (senão upload rejeitado); `versionName` p/ humanos.
- **Staged rollout**: X% → monitora crash/ANR → sobe; pode **halt**.

## 15. Multi-Repo
- Multi = autonomia/isolamento (mudança atômica difícil, versionar artefatos) × Mono = atômico fácil.
- **SemVer**: MAJOR quebra · MINOR compatível · PATCH corrige. Consuma versão fixa, não branch.

## 16. Acessibilidade (WCAG 2.1 AA)
- ⚠️ **Contraste: 4.5:1 = AA** (3:1 texto grande); **7:1 = AAA**.
- ⚠️ **Alvo de toque 44px = AAA** (2.5.5). O mínimo **AA (24px) é 2.5.8, só na WCAG 2.2**.
- Não passe info só por cor (1.4.1). `contentDescription` (null p/ decorativo), `semantics { heading() }`.

## 17. AWS Mobile
- **Cognito User Pool** (login, JWT) × **Identity Pool** (troca por credenciais AWS temporárias/STS).
- S3: **presigned URL** do backend, ou STS. ❌ Access Key no APK.
- DynamoDB: modele pelo **access pattern** (PK/SK), não por normalização.

## 18. DevOps
- **Quality gate** (Sonar) barra o merge pelo **código novo** ("clean as you code").
- ⚠️ Segredos em **variáveis protegidas/masked** do CI, nunca no repo.
- GitLab CI (YAML no repo) × Jenkins (servidor central + plugins).

## 19. Qualidade (Testes)
- **Pirâmide**: muitos unit → integração → poucos E2E. ⚠️ anti-padrão = cone de sorvete.
- **Stub** (respostas fixas) × **Mock** (verifica interação) × **Fake** (impl real simplificada).
- Flaky → quarentena + achar causa + tornar determinístico.

## 20. Ágil & Soft Skills
- **DoR** (entrar na sprint) × **DoD** (estar pronta). Daily = time se sincroniza, **não** é status pro chefe.
- **Velocity** = capacidade p/ planejar, **não** meta nem comparação entre times.
- **Comportamental → STAR**: Situação, Tarefa, **Ação (1ª pessoa)**, Resultado **mensurável**.

## 21. Java Backend (diferencial)
- Camadas: `@RestController` (HTTP) → `@Service` (regra + transação) → `@Repository` (dados). Exponha **DTO**, não a entidade.
- ⚠️ **N+1** em JPA → `JOIN FETCH`/`@EntityGraph`/batch. Idempotentes: GET/PUT/DELETE (POST não).

---

### 🎯 Frases de efeito (senioridade)
- "A máquina valida a máquina — sem gate, velocidade do agente vira dívida técnica."
- "Meça antes de otimizar; o gargalo raramente é onde a intuição aponta."
- "Dado que vem de ferramenta é dado, não instrução."
- "Confiabilidade de agente é **medida** (evals), não sentida."
- "Prefiro **fake** a mock: testa comportamento e sobrevive a refactor."

*Boa sorte. Respira, escuta a pergunta até o fim, e responde com trade-off — não só a resposta.* 🍀
