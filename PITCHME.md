## ServiceNow Best Practices

---

#### Table

@ul
- Verificar se existe uma tabela OOB com o mesmo objetivo no Product Documentation
- Nome no singular
- Nome em inglês
- Nome informativo
- Desativar ou selecionar um Application Module
- Desativar ou selecionar a role correta para ACL
@ulend

+++

@ul
- Prefixo de módulo
  - Prefixo Many 2 Many
  - Prefixo Data Lookup
  - Prefixo de herança
@ulend

---

#### Dictionary Entry

@ul
- Verificar se existe uma tabela OOB com o mesmo objetivo no Product Documentation
- Causa problemas em tabelas filhas ?
- Não criar campo na tabela Task
- Nome no singular
- Nome do campo não repete o nome da tabela onde está desnecessariamente (ex: `u_task_number` na tabela `task`)
@ulend

+++

@ul
- Nome sem redundância (ex: se é do tipo date não precisa se chamar `u_evaluation_date`)
- Se for tabela filha verificar se pode ser feito com Dictionary Overrides
- Se a lista de valores exibidas em uma choice depende do valor de outro campo use **Dependant Value**
- Não mudar field type (caso seja imprescindível, analisar o impacto e desenvolver fix scripts necessários)
@ulend

---

#### Field Label

@ul
- Criar as traduções (se a instância for multi idioma)
- Evite labels no formato de perguntas (ex: `When will this be activated?` 👉 `Activation Date`)
- Escrever Hints
- Choice values em `snake_case`
- Se choices em campos de tabelas diferentes possuem as mesmas opções (por exemplo lista de UF), use Reference Choice List Field para "emprestar" as choices
@ulend

---

#### Form Layout

@ul
- O layout está decente
- A informação está bem estruturada
- Pode ser lida do top to bottom seguindo o fluxo do processo
- Os campos mais importantes estão no topo
- Use 100% width apenas em campos de texto comprido
- Use form sections para agrupar infomação relacionada
@ulend

+++

@ul
- Use form sections caso haja muito scroll na página
- O formulário é o mais curto e resumido que ele pode ser ?
- Use form annotations para explicar a intenção dos campos (pode ser desabilitado por usuários experientes)
- Use o texto da form annotation como chave para tradução na `System Messages`
@ulend

---

#### Form View

@ul
- Client Scripts e UI Policies especializadas (específica de uma view) tornam a página mais rápida
- Use **View Rules** para definir a view atual a partir do estado do registro
@ulend

---

#### List Layout

@ul
- Evite scroll horizontal a todo custo
- Evite campos de texto volumoso, html e journal
- Considere os botões do List Control com cautela
- Não use campos de referência como a primeira coluna da lista (pois o usuário clica nele e será redirecionado para uma página que não queria)
@ulend

---

#### UI Action

@ul
- Crie um botão ou link para o usuário executar a ação ao invés de pedir para que atualize um campo e dê um update
- Inclua `current.canWrite()` nas condições
- Inclua `current.active` nas condições
- Use **Form Buttons** para as ações mais relevantes
@ulend

+++

@ul
- Use **Context Menu** ou **Link Action** para as ações menos relevantes
- Não crie muitos form buttons concorrentes pois não servirá no layout do formulário
- Não inclua muita lógica no código da UI Action, deixe que uma **Business Rule** cuide da operação
- Se a lógica existe em mais de um lugar use um script include
@ulend

---

#### Application Navigator

@ul
- Use **Section Separator** para agrupar módulos relacionados
- **Certifique-se de que as roles estão corretas**
- Use nomes de módulo consistentes
- Use **Application Categories** para segregação visual usando cores (**não exagerar**)
- Use **Interceptors** para manter os application menus enxutos
@ulend

---

#### Security

@ul
- Use **Roles** como método primário de segurança (melhor manutenção e performance)
- Considere essa ACL como ponto de partida:
  - Read: todo mundo
  - Write: apenas uma role
  - Delete: apenas admin (especialmente se alguma tabela tem referência para ela)
@ulend

+++

@ul
- **Client Script** e **UI Policy** são *facilmente* hackeáveis
- Gerar Data Policies a partir de UI Policies se necessário
@ulend

---

#### Business Rule

@ul
- Não use `current.update()`
- Considere usar `async` quando a informação não precisa ser exibida imeiatamente (processar email, calcular SLA, gerar report)
- [ ] Use **Before Rules** para validação dos dados (seguido de `currrent.setAbortAction(true)`
- [ ] Ao comparar dois campos analisar se deve verificar se não são vazios
@ulend

---

#### Client Side Scripts

@ul
- [ ] **Client Script** e **UI Policy** rodam *apenas* em formulário
- [ ] Use `g_scratchpad` ao invés de consultas no servidor em client script `onLoad()`
- [ ] Deixe o campo `readonly` enquanto realiza uma chamada para o servidor
- [ ] Não use chamadas síncronas
- [ ] Evite usar `g_form.getReference()` e `GlideRecord` no client pois buscam todos os dados da tabela e impactam performance
@ulend

+++

@ul
- [ ] Evite manipulação no DOM
- [ ] Procure usar métodos do `g_form` como `getControl()` e `setValue()` pois contornam incompatibilidades entre browsers
- [ ] Quando usar `g_form.setValue()` em campo do tipo referência, lembre-se de usar o terceiro parâmetro (display value)
- [ ] **Client Script** executam antes de **UI Policy**, portanto ela vai valer por último no estado dos campos
- [ ] Evite client scripts globais
@ulend


