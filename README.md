
promised-ldap
=============

Esta biblioteca é um shim rápido e sujo sobre [ldapjs] (https://github.com/mcavage/node-ldapjs) para adicionar
promessas e autenticação mais fácil. Atualmente, ele suporta apenas a interface do cliente.

Uso
-----

Para criar um novo cliente:

```js
var LdapClient = require('promised-ldap');
var client = new LdapClient({url: 'ldap://127.0.0.1:389'});
```

O argumento de opções para o construtor é idêntico ao argumento de opções usado em `ldapjs` para
`ldap.createClient`.  Por favor, veja os documentos [aqui](http://ldapjs.org/client.html).

Ele suporta os outros métodos documentados lá também, exceto que, em vez de receber um retorno de chamada, os métodos
devolver uma promessa.  E.g.:

```js
client.bind('username', 'password').then(function () { ... });
```

O método `search` em` ldapjs` agora está acessível (na forma prometida) por `client._search`. Eu forneci um método `client.search` 
com comportamento semelhante ao original, mas com uma API mais fácil.  E.g.:

```js
client.search(base, options).then(function (result) {
  /* result is:
  {
    entries: [...],
    references: [...]
  }
  no messing about with EventEmitters!
  */
});
```

Vamos enfrentá-lo, o motivo pelo qual você está mexendo com o LDAP é provavelmente para adicionar autenticação 
LDAP ao seu aplicativo, então adicionei alguns métodos auxiliares para isso:

```js
client.authenticate(base, cn, password).then(function (result) {
  // if the authentication succeeded, then result is the LDAP user object
  // otherwise, it is null
});
```

Isso basicamente faz um `bind` usando as credenciais fornecidas e, se for bem-sucedido, faz uma` search` para 
o usuário especificado e retorna isso....

Se tudo o que você realmente deseja é o nome do usuário, endereço de e-mail e lista de grupos não qualificados, 
também podemos fazer isso:

```js
client.authenticateUser(base, cn, password).then(function (result) {
  /*
  if the authentication succeeded, then result is:
  {
    name: <the user's DisplayName>,
    email: <the user's PrincipalName>,
    groups: <an array of the CNs of groups which are in the base CN>
  }
  otherwise, result is null
  */
});
```

Solicitações e sugestões de pull são bem-vindas!
