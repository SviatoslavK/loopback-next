---
lang: en
title: 'Add TodoListImage Relation'
keywords: LoopBack 4.0, LoopBack 4
sidebar: lb4_sidebar
permalink: /doc/en/lb4/todo-list-tutorial-has-one-relation.html
summary: LoopBack 4 TodoList Application Tutorial - Add TodoListImage Relation
---

We have that a `Todo` [`belongsTo`](../../BelongsTo-relation.md) a `TodoList`
and a `TodoList` [`hasMany`](../../HasMany-relation.md) `Todo`s. Another type of
relation we can add is [`hasOne`](../../hasOne-relation.md). To do so, let's add
`TodoListImage` such that each `TodoList` `hasOne` image.

### Create the Model

Similar to how we created the model for
[`TodoList`](todo-list-tutorial-model.md), using `lb4 model`:

```sh
lb4 model
? Model class name: TodoListImage
? Please select the model base class Entity (A persisted model with an ID)
? Allow additional (free-form) properties? No
Model TodoListImage will be created in src/models/todo-list-image.model.ts

Let's add a property to TodoListImage
Enter an empty property name when done

? Enter the property name: id
? Property type: number
? Is id the ID property? Yes
? Is id generated automatically? No
? Is it required?: No
? Default value [leave blank for none]:

Let's add another property to TodoListImage
Enter an empty property name when done

? Enter the property name: value
? Property type: string
? Is it required?: Yes
? Default value [leave blank for none]:

Let's add another property to TodoListImage
Enter an empty property name when done

? Enter the property name:
   create src/models/todo-list-image.model.ts
   update src/models/index.ts

Model TodoListImage was created in src/models/
```

### Create the Repository

Using `lb4 repository`, let's create the repository:

```sh
lb4 repository
? Please select the datasource DbDatasource
? Select the model(s) you want to generate a repository TodoListImage
? Please select the repository base class DefaultCrudRepository (Legacy juggler bridge)
   create src/repositories/todo-list-image.repository.ts
   update src/repositories/index.ts

Repository TodoListImageRepository was created in src/repositories/
```

### Add the Relation

First, let's add the relation to the model classes:

{% include code-caption.html content="src/models/todo-list-image.model.ts" %}

```ts
import {belongsTo} from '@loopback/repository';
import {TodoList, TodoListWithRelations} from './todo-list.model';

@model()
export class TodoListImage extends Entity {
  // ... other properties

  @belongsTo(() => TodoList)
  todoListId: number;

  // ...
}

export interface TodoListImageRelations {
  todoList?: TodoListWithRelations;
}
```

{% include code-caption.html content="src/models/todo-list.model.ts" %}

```ts
import {hasOne} from '@loopback/repository';
import {
  TodoListImage,
  TodoListImageWithRelations,
} from './todo-list-image.model';

@model()
export class TodoList extends Entity {
  // ... other properties

  @hasOne(() => TodoListImage)
  image?: TodoListImage;

  // ...
}

export interface TodoListRelations {
  todos?: TodoWithRelations[];

  // Add the following line
  image?: TodoListImageWithRelations;
}
```

Next, let's add the relation to the repository classes:

{% include code-caption.html content="src/repositories/todo-list.repository.ts" %}

```ts
// Add the following imports
import {HasOneRepositoryFactory} from '@loopback/repository';
import {TodoListImage} from '../models';
import {TodoListImageRepository} from './todo-list-image.repository';

export class TodoListRepository extends DefaultCrudRepository<
  TodoList,
  typeof TodoList.prototype.id,
  TodoListRelations
> {
  // other code

  // Add the following
  public readonly image: HasOneRepositoryFactory<
    TodoListImage,
    typeof TodoList.prototype.id
  >;

  constructor(
    // other code

    // Add the following
    @repository.getter('TodoListImageRepository')
    protected todoListImageRepositoryGetter: Getter<TodoListImageRepository>,
  ) {
    // other code

    // Add the following
    this.image = this.createHasOneRepositoryFactoryFor(
      'image',
      todoListImageRepositoryGetter,
    );

    this.registerInclusionResolver('image', this.image.inclusionResolver);
  }
}
```

```ts
import {BelongsToAccessor} from '@loopback/repository';
import {TodoList} from '../models';
import {TodoListRepository} from './todo-list.repository';

export class TodoListImageRepository extends DefaultCrudRepository<
  TodoListImage,
  typeof TodoListImage.prototype.id,
  TodoListImageRelations
> {
  // Add the following
  public readonly todoList: BelongsToAccessor<
    TodoList,
    typeof TodoListImage.prototype.id
  >;
  constructor(
    // other code

    // Add the following line
    protected todoListRepositoryGetter: Getter<TodoListRepository>,
  ) {
    // other code

    // Add the following
    this.todoList = this.createBelongsToAccessorFor(
      'todoList',
      todoListRepositoryGetter,
    );

    this.registerInclusionResolver('todoList', this.todoList.inclusionResolver);
  }
}
```

{% include note.html content="
We are working on adding `hasOne` to the CLI command `lb4 relation`. See [issue #2980](https://github.com/strongloop/loopback-next/issues/2980).
" %}

### Create the Controller

Create a new file `src/controllers/todo-list-image.controller.ts`. We only want
to access a `TodoListImage` through a `TodoList`, so we'll create `GET` and
`POST` methods that allow for that as follows:

{% include code-caption.html content="src/controllers/todo-list-image.controller.ts" %}

```ts
import {repository} from '@loopback/repository';
import {get, getModelSchemaRef, param, post, requestBody} from '@loopback/rest';
import {TodoListImage} from '../models';
import {TodoListRepository} from '../repositories';

export class TodoListImageController {
  constructor(
    @repository(TodoListRepository) protected todoListRepo: TodoListRepository,
  ) {}

  @post('/todo-lists/{id}/image', {
    responses: {
      '200': {
        description: 'create todoListImage model instance',
        content: {
          'application/json': {schema: getModelSchemaRef(TodoListImage)},
        },
      },
    },
  })
  async create(
    @param.path.number('id') id: number,
    @requestBody() image: TodoListImage,
  ): Promise<TodoListImage> {
    return this.todoListRepo.image(id).create(image);
  }

  @get('/todo-lists/{id}/image', {
    responses: {
      '200': {
        description: 'The image belonging to the TodoList',
        content: {
          'application/json': {
            schema: getModelSchemaRef(TodoListImage, {includeRelations: true}),
          },
        },
      },
    },
  })
  async find(@param.path.number('id') id: number): Promise<TodoListImage> {
    return this.todoListRepo.image(id).get();
  }
}
```
