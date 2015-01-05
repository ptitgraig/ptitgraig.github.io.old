---
layout: post
title: "Memento Backbone.js"
date: 2014-02-05 17:26
comments: true
categories: Dev
---

Backbone est un framework javascript MVC qui permet de construire des applications de type Single-Page-Webapp.

En vérité il s'agit plutôt d'un framework MV* car le Controller n'existe pas vraiment dans Backbone.

Faisons un parrallèle entre un MVC classique et un MVC Backbone.

## Models

- C'est là ou se fait la validation des données.
- Sont persistés soit par du localStorage ou bien par synchronisation avec une base de données.
- Plusieurs Views peuvent observer des changements sur un Model. Une View, en effet, regarde un Model et peut ainsi savoir quand un Model a changé et afficher les infos à jour du Model.
- Un ensemble de Models est appelé une Collection. Dès qu'un Model dans cette Collection change, la View attachée à ce Model est notifiée.

## Controllers

- N'existe pas. La View joue le rôle de Controller.
- Gérer les changements effectués dans la vue par l'utilisateur
- Mettre à jour le Model quand un utilisateur a terminé ses actions

## Views

- C'est là que les utilisateurs cliquent et font mumuse.
- Donc c'est là qu'on lit / edite / supprime les données d'un Model.
- Il y a une fonction `render()` qui doit s'occuper d'afficher le contenu du Model (ça utilise le moteur de template de Underscore.js). On l'utilise en callback afin de notifier la View de potentiels changements sur le Model.
- Dans le code, on fait référence à la vue avec `this.el`
- Quand un utilisateur clique dans la View, c'est normalement le Controller qui s'occupe du traitement a effectuer, mais comme on l'a vu, il n'y a pas de Controller dans Backbone. Les évenements sont gérés dans la View grâce à un objet `event : {}`


## pense-bête pour le Model

### Méthodes native à Backbone 

- set : pour changer ou initialiser un attribut du Model `myModel.set('toto', 'un valeur');`
- get : pour obtenir la valeur d'un attribut du Model `myModel.get('toto');`
- validate : permet de valider le model ex :
```
validate: function(attributes){
	if(attributes.title === undefined){
	    return "Remember to set a title for your todo.";
	}
},
```
### ecouter si le modèle change : 
```
this.on('change', function(){ 
	//faire quelquechose
});
```
### ecouter si l'attribut toto du model change : `this.on('change:toto')`

## pense-bête pour la View

La méthode `render()` de la View peut être liée à la méthode `change()` du Model. Ainsi dès qu'il y a un changement dans le Model, la View refléte immédiatement ces changements.

### setElement()

Colle un template HTML a une vue.

```
var view = new Backbone.View;
view.setElement('<p><a><b>test</b></a></p>');
view.el // <p><a><b>test</b></a></p>
```

### render()

A la fin de render() il est bien de faire `return this` car :

- la View peut être réutiliser par des View parent
- Créer une liste d'éléments sans repeindre chacun individuellement, l'élément est déssiné une fois par le navigateur et la liste entière est remplie en données (sens peu claire)

## pense-bête Collection

### Syntaxe
```
var Todo = Backbone.Model.extend({});

var TodosCollection = Backbone.Collection.extend({
  model: Todo
});
```

### Ajout suppression de Models

Une fois la Collection créée, on peut supprimer ou ajouter des Models à la Collection avec : `add()` et `remove()`. Ces 2 méthodes acceptent un ou plusieurs model en paramètre (sous forme de tableau [])

### Retrouver un Model

`Collection.get(id)` avec id l'id du Model

```
var myTodo = new Todo({title:'Read the whole book', id: 2});
var todos = new TodosCollection([myTodo]);
var todo2 = todos.get(2);
```

Afin d'intentifier le Model récupéré on peut utiliser : `id` `cid` `idAttribute`

Chaque Model a un `id` qui est unique (type Integer ou String)
`idAttribute` est l'attribut qui identifie le Model dans la database (`id` dans la base de donnée). Ca dit a Backbone quel champ de la base de donnée doit être remplir avec la propriété `id`. Exemple `userId` dans la base donnera un `userId` dans la définition du Model dans Backbone.

### Ecouter des événements

```
var TodosCollection = new Backbone.Collection();
TodosCollection.on("change:title", function(model) {
    console.log("J'ai changer d'avis, je devrais " + model.get('title'));
});
TodosCollection.add([
  { title: 'go to Jamaica.', completed: false, id: 3 },
]);
var myTodo = TodosCollection.get(3);
myTodo.set('title', 'aller nager');
```

Va afficher : J'ai changer d'avis, je devrais aller nager

On peut écouter si il y a du changement dans une collection (ajout suppression modification) :

```
TodosCollection.on("add", function(model) {
  console.log("Ajouté " + model.get('title'));
});
TodosCollection.on("remove", function(model) {
  console.log("Supprimé " + model.get('title'));
});

TodosCollection.on("change:completed", function(model) {
  console.log("Changement de l'attribute completed " + model.get('title'));
});
TodosCollection.set([
    { id: 1, title: 'go to Jamaica.', completed: true },
    { id: 2, title: 'go to China.', completed: false },
    { id: 4, title: 'go to Disney World.', completed: false }
]);
```

Vider une Collection : `myCollection.reset()`

Ecouter le vidage d'une Collection : myCollection.on("reset", function)

Faire un `set()` d'une Collection peut provoquer des `remove()` ou `add()` en fonction de ce qui est setté

Fonctionnalités hérité d'Underscore :
- `forEach`
- `sortBy`
- `map`
- `min/max` :
```
Todos.max(function(model){
  return model.id;
}).id;
```
- `pluck`

`chain()` permet de chainer l'appel de fonction sur une Collection

```
var collection = new Backbone.Collection([
  { name: 'Tim', age: 5 },
  { name: 'Ida', age: 26 },
  { name: 'Rob', age: 55 }
]);

var filteredNames = collection.chain() // start chain, returns wrapper around collection's models
  .filter(function(item) { return item.get('age') > 10; }) // returns wrapped array excluding Tim
  .map(function(item) { return item.get('name'); }) // returns wrapped array containing remaining names
  .value(); // terminates the chain and returns the resulting array

console.log(filteredNames); // logs: ['Ida', 'Rob']
```

## Persistence RESTful

### Récupérér un Model du serveur

`Collection.fetch()` retrouve un ensemble de Models du server sous la form d'un tableau JSON en envoyant une requête HTTP GET vers l'URL spécifié par la propriété `url` de la Collection. Quand les données sont récupérées, la fonction `set()` va être exécuté pour mettre à jour la Collection.

### Sauvegarder un Model sur le serveur

On utilise la méthode `save()` du Model que l'on veut sauvegarder.
- Quand `save()` est appelé sur un Model qui a été récupérer du serveur avec la méthode `fetch()`, un requête HTTP PUT est envoyée.
- Si le Model est une nouvelle instance, une requête HTTP POST est envoyé à l'URL de la Collection.

### Supprimer un Model du serveur

Avec `destroy()` : envoie une requête HTTP DELETE

### Options

Il est possible de passer seulement les choses que l'on veut mettre à jour dans un Model et pas forcememnt tout le Model (requête HTTP PATCH). Par exemple :
```
// Sauvegarde partielle avec PATCH
model.clear().set({id: 1, a: 1, b: 2, c: 3, d: 4});
model.save();
model.save({b: 2, d: 4}, {patch: true});
console.log(this.syncArgs.method);
// 'patch'
```

## Events

### on() off() trigger()

`Backbone.Events` peut donner à un objet la capacité de lié et déclencher des événements personnalisés.

`on()` lie une fonction callback à un objet. Le callback est appelé à chaque fois que l'événement est déclenché.

```
var myObject = {};

// Mixin
_.extend(myObject, Backbone.Events);

// Ajouter un événement personnalisé
myObject.on('dance', function(msg){
  console.log('Déclenchement de ' + msg);
});

// Trigger the custom event
myObject.trigger('dance', 'our event');
```

`off()` supprime toutes les fonctions de callback sur un objet

```
// Removes event bound to the object
myObject.off("dance:tap");
```

Pour supprimer une callback en particulier :

```
var ourObject = {};

// Mixin
_.extend(ourObject, Backbone.Events);

function dancing (msg) { console.log("We are dancing. " + msg); }
function jumping (msg) { console.log("We are jumping. " + msg); }

// Add two listeners to the same event
ourObject.on("move", dancing);
ourObject.on("move", jumping);

// Trigger the events. Both listeners are called.
ourObject.trigger("move", "Yeah!");

// Removes specified listener
ourObject.off("move", dancing);

// Trigger the events again. One listener left.
ourObject.trigger("move", "Yeah, jump, jump!");
```

`trigger()` peut passer plusieurs arguments à une fonction callback

```
var ourObject = {};

// Mixin
_.extend(ourObject, Backbone.Events);

function doAction (action, duration) {
  console.log("We are " + action + ' for ' + duration ); 
}

// Add event listeners
ourObject.on("dance", doAction);
ourObject.on("jump", doAction);
ourObject.on("skip", doAction);

// Passing multiple arguments to single event
ourObject.trigger("dance", 'dancing', "5 minutes");

// Passing multiple arguments to multiple events
ourObject.trigger("dance jump skip", 'on fire', "15 minutes");
```

`listenTo()` et `stopListening()` permet à un objet {d'écouter/ arrêter d'écouter} les événements sur un autre objet.

*Chaque fois qu'on fait `on()` sur un objet on doit faire aussi `off()`*, sinon quand on supprimer un Model il va rester en mémoire. `View.remove()` fait un appel à `stopListening()` afin de s'assurer que tous les écouteurs sont déliées.

### Events et Views

2 types d'événements peuvent être lié à une View :
- événements DOM (via jQuery.on() this va faire référence à l'objet DOM)
- événements déclenché via une API Event (via la propriété events{} this fait référence à la View)

## Routers

Connecte des URLs à des parties de l'application. Nécessaire si tu veux que certaines parties de ton application soient : mise dans les favoris, partageable, si tu veux pouvoir faire un Back avec le bouton back du navigateur.

```
var TodoRouter = Backbone.Router.extend({
	routes: {
		"search/:query/p:page" : "searchTodos"
		// http://example.com/#search/job/p1
	}
});
var myTodoRouter = new TodoRouter();
```
`Backbone.history.start();` dit à Backbone qu'il fait observer tout changement de hash dans l'URL (d'où le /# hyper vitale)


















