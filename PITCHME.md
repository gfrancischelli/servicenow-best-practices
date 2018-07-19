## ServiceNow Best Practices

---

### Table

@ul
- Nome no singular
- Nome em inglês
- Desativar ou selecionar um Application Module
- Desativar ou selecionar a role correta para ACL
- Verificar se existe uma tabela OOB com o mesmo objetivo no Product Documentation
@ulend

+++

@ul
- Nome informativo
  - Prefixo de módulo
  - Prefixo Many 2 Many
  - Prefixo Data Lookup
  - Prefixo de herança
@ulend

---

### Dictionary Entry

@ul
- Nome no singular
- Nome sem redundância 
- Causa problemas em tabelas filhas ?
- Não criar campo na tabela Task
@ulend

+++

@ul
- Se for tabela filha verificar se pode ser feito com Dictionary Overrides
- Se a lista de valores exibidas em uma choice depende do valor de outro campo use **Dependant Value**
- Não mudar field type (caso seja imprescindível, analisar o impacto e desenvolver fix scripts necessários)
- Verificar se existe um campo OOB com o mesmo objetivo no Product Documentation
@ulend

---

### Field Label

@ul
- Criar as traduções (se a instância for multi idioma)
- Evite labels no formato de perguntas (ex: `When will this be activated?` 👉 `Activation Date`)
- Escrever Hints
- Choice values em `snake_case`
- Se choices em campos de tabelas diferentes possuem as mesmas opções, use Reference Choice List Field para "emprestar" as choices
@ulend

---

### Form Layout

@ul
- Pode ser lida do top to bottom seguindo o fluxo do processo
- Os campos mais importantes estão no topo
- O formulário é o mais curto e resumido que ele pode ser ?
- Use 100% width apenas em campos de texto comprido
@ulend

+++

@ul
- Use form sections para agrupar infomação relacionada
- Use form sections caso haja muito scroll na página
- Use form annotations para explicar a intenção dos campos 
- Use o texto da form annotation como chave para tradução na **System Messages**
@ulend

---

### Form View

@ul
- Client Scripts e UI Policies especializadas (específica de uma view) tornam a página mais rápida
- Use **View Rules** para definir a view atual a partir do estado do registro
@ulend

---

### List Layout

@ul
- Evite scroll horizontal a todo custo
- Evite campos de texto volumoso, html e journal
- Considere os botões do List Control com cautela
- Não use campos de referência como a primeira coluna da lista (pois o usuário clica nele e será redirecionado para uma página que não queria)
@ulend

---

### UI Action

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

### Scripting

@ul
- Use nomes informativos nas variáveis
- Use a identação do ServiceNow
- Ao usar dot-walking, verifique se o valor existe
- Evite queries `GlideRecord` em **ACL** (devido ao impacto significativo)
- Escreva módulos pequenos
@ulend

+++

@ul
- Evite funções que se extendem por dezenas de linhas
- Evite deep nesting de `if`s pois prejudica legibilidade e entendimento
- Use comentários para explicitar o **porquê** do seu código ser o que é
- Evite queries complexas em data sets muito grandes
- Considere exercitar uma POC em sua instância pessoal 
@ulend

+++

@ul
- Escreva funções que retornem um valor
- Evite valores hard-coded, utilize **System Properties** para paramatrizar o funcionamento do script
- Evite usar `sys_id` sem comentar o que é ou armazenar em variável com nome auto explicativo
@ulend

+++

@ul
- Use `gr.getDisplayValue()` ao invés de `gr.name` por ser a prova de futuro
- Evite "truques inteligentes". Explicito é melhor que implicito
- Ao contar quantidade de registros use `GlideAggregate` ao invés de `gr.getRowCount()`
- Evite dot-walking desnecessário em glide records pois realizam queries adicionais no banco de dados
@ulend

+++

@ul
- Jamais use `eval()`. Prefira `GlideEvaluator.evaluateString()` caso necessário
- Ao desativar um script ou realizar mudanças impactantes documente o motivo no campo description
- Alterar a flag `active` não impede que o sistema faça um upgrade no registro OOB
- Se for preciso alterar uma tabela OOB, desative pelo campo `active` e duplique para editar
@ulend

+++

### ***Sempre*** use getters e setters. `gr.getValue("name")` > `gr.name`

+++

```javascript
var arr = []
var gr = new GlideRecord("incident");
gr.addQuery("active", true);
gr.setLimit(3)
gr.query();
while(gr.next()) {
  arr.push(gr.sys_id);
}
gs.print(arr);

"01f29ce2db1797007a1d9d40ba9619e1,01f29ce2db1797007a1d9d40ba9619e1,01f29ce2db1797007a1d9d40ba9619e1"
```

---

### GlideRecord Queries

+++

### Use `gr.addActiveQuery()`

+++

### Ordene as queries da mais rápida para mais lenta

```javascript
var incidentGr = new GlideRecord("incident");
incidentGr.addActiveQuery();
incidentGr.addNullQuery("assignment_group");
incidentGr.addQuery("short_description", "CONTAINS", "windows")
incidentGr.addQuery("sys_created_on", ">", gs.daysAgo(2));
```

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

### Business Rule

@ul
- Não use `current.update()`
- Considere usar `async` quando a informação não precisa ser exibida imeiatamente (processar email, calcular SLA, gerar report)
- Use **Before Rules** para validação dos dados (seguido de `currrent.setAbortAction(true)`
- Ao comparar dois campos analisar se deve verificar se não são vazios
@ulend

---

### Client Side Scripts

@ul
- **Client Script** e **UI Policy** rodam *apenas* em formulário
- Use `g_scratchpad` ao invés de consultas no servidor em client script `onLoad()`
- Deixe o campo `readonly` enquanto realiza uma chamada para o servidor
- Não use chamadas síncronas
- Evite usar `g_form.getReference()` e `GlideRecord` no client pois buscam todos os dados da tabela e impactam performance
@ulend

+++

@ul
- Evite manipulação no DOM
- Procure usar métodos do `g_form` como `getControl()` e `setValue()` pois contornam incompatibilidades entre browsers
- Quando usar `g_form.setValue()` em campo do tipo referência, lembre-se de usar o terceiro parâmetro (display value)
- **Client Script** executam antes de **UI Policy**, portanto ela vai valer por último no estado dos campos
- Evite client scripts globais
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

