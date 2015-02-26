# Cake 3 Quickstart

This is a quick, extremely high-level explanation of the pieces in a CakePHP App.

There are 3 major divisions of code. [M](#model)odels, [V](#view)iews, and [C](#controller)ontrollers which is why it's considered an MVC framework.

- **Models** handle Data access + Data Storage
  - _use **Behaviors** for reusable code_
- **Views** handle Data presentation + User interaction
  - _use **Helpers** for reusable code_
- **Controllers** handle Parsing requests + Building responses
  - _use **Components** for reusable code_

CakePHP 3 is written according to standardized [Code Conventions](http://book.cakephp.org/3.0/en/intro/conventions.html) and uses a standardized [Folder Structure](http://book.cakephp.org/3.0/en/intro/cakephp-folder-structure.html)

Fully-documented [API](http://api.cakephp.org/3.0/index.html) and [Book](http://book.cakephp.org/3.0/en/index.html)

## Model

Models do all the business logic. They are the ***ONLY*** objects who know where and how your data is stored, and they're the only ones who can directly access it. Often the **fattest** files in your app because they're really the only ones doing any work. They **must** be the **sole authority** on the object they represent; `Ewoks` should no nothing of how `Lightsabers` work.

Models are collectively made up of [Tables](#table) and [Entities](#entity) and can be optionally governed by [Behaviors](#behavior).

##### Entity
An `Entity` represents a single record (e.g from the database) [[link]](http://book.cakephp.org/3.0/en/orm/entities.html#namespace-Cake\ORM)

- **Entity Setup**
  - `namespace App\Model\Entity`
  - `use Cake\ORM\Entity`
  - `class Ewok extends Entity` singular, not suffixed  [[link]](http://book.cakephp.org/3.0/en/orm/entities.html#creating-entity-classes)
  - `src/Model/Entity/Ewok.php` [[link]](http://book.cakephp.org/3.0/en/orm/entities.html#creating-entity-classes)
- **Entity Fields**
  - `$ewok->get('name')`, `$ewok->name` and `$ewok->name()` all retrieve the Ewok's name. [[link]](http://book.cakephp.org/3.0/en/orm/entities.html#accessing-entity-data)
  - custom mutators `function _setColor($color) { .. }` and `function _getColor(){ .. }` [[link]](http://book.cakephp.org/3.0/en/orm/entities.html#accessors-mutators)
  - `$ewok->set(['name'=> .., 'weight' => .., ..])` mass-assign entity properties [[link]](http://book.cakephp.org/3.0/en/orm/entities.html#accessing-entity-data)
  - `protected $_accessible = [..'field'=>true, '*'=>false, ..]` allow or disallow a field (or `'*'` for all) to be mass-updated via `$ewok->set([..])` [[link]](http://book.cakephp.org/3.0/en/orm/entities.html#mass-assignment)
- **Entity Virtual Fields**
  - virtual properties let you create fields at runtime
  - `$this->_getName(){ return $this->first_name . ' ' $this->last_name }` available as `$ewok->name`
  - `protected $_virtual = [ ..,'name',.. ]` expose virtual fields on serialization (array, JSON, XML, etc) [[link]](http://book.cakephp.org/3.0/en/orm/entities.html#exposing-virtual-properties)
- **Entity Serialization**
  - `$ewok->toArray()` or `json_encode($ewok)` [[link]](http://book.cakephp.org/3.0/en/orm/entities.html#converting-to-arrays-json)
  - `protected $_hidden = [ ..,fields,.. ];` hide fields on entity serialization (array, JSON, XML, etc) [[link]](http://book.cakephp.org/3.0/en/orm/entities.html#hiding-properties)
  

##### Table
A `Table` represents the structure that holds the records; a collection of [Entities](#entity). All Table methods act on groups of data, or the general data structure itself. `$ewok->setHidden(true)` would be an [Entity](#entity) method, but then the Empire (a Table) comes along and does `$ewoks->find('hidden')` and gets em all.

- **Table Setup**
  - `namespace App\Model\Table;`
  - `use Cake\ORM\Table;`
  - `class EwoksTable extends Table` plural, suffixed [[link]](http://book.cakephp.org/3.0/en/orm/table-objects.html#basic-usage)
  - `src/Model/Table/EwoksTable.php` file location
- **Table Behaviors**
  - `$this->addBehavior('Kickable', [..custom settings..])` tell Table to [Behave](#behavior) a certain way
- **Table Relations (Associations)**
  - `$this->belongsTo('Tribes')` plural, no prefix - can associate with other tables [[link]](http://book.cakephp.org/3.0/en/orm/associations.html)
- **Table Methods**
  - `$ewoks->get('wicket-w-warrick')` will return an `$ewok` [Entity](#entity) where `$ewok->id ` is `'wicket-w-warrick'` [[link]](http://book.cakephp.org/3.0/en/orm/retrieving-data-and-resultsets.html#getting-a-single-entity-by-primary-key)
  - lifecycle callbacks like [beforeSave()](http://book.cakephp.org/3.0/en/orm/table-objects.html#beforesave) and [afterSave()](http://book.cakephp.org/3.0/en/orm/table-objects.html#aftersave) [[link]](http://book.cakephp.org/3.0/en/orm/table-objects.html#lifecycle-callbacks) 

##### Behavior
Behaviors are ways for [Models](#model) to re-use common code. Same idea as the [Controller](#controller)-layer version called [Components](#component) and the [View](#view)-layer version, called [Helpers](#helper).

- **Behavior Setup**
  - `namespace App\Model\Behavior`
  - `use Cake\ORM\Behavior`
  - `class RebelBehavior extends Behavior` suffixed
  - `src/Model/Behavior/RebelBehavior.php` 
- **Behavior Usage**
  - e.g. if all post titles should be converted to slugs before saving to database [[link]](http://book.cakephp.org/3.0/en/orm/behaviors.html#creating-a-behavior)
  - e.g. if security table data is stored/retrieved in Tree format [[link]](http://book.cakephp.org/3.0/en/orm/behaviors/tree.html#namespace-Cake\ORM\Behavior)
  - Behaviors can hook into Model [Table](#table) lifecycle callbacks [[example]](http://book.cakephp.org/3.0/en/orm/behaviors.html#defining-event-listeners)

## View
The View layer handles actually presenting stuff to the user - drawing the forms, layouts, all the pretty things, as well as making requests to the [Controller](#controller) layer.

The View layer is collectively made up of [Layouts](#layout), [View Blocks](#view-block), [Templates](#template), [Elements](#element), and [Cells](#cell).

##### Layout
A Layout is the highest level of the View layer. It can contain [View Blocks](#view-block), a [Template](#template), [Elements](#element), and [Cells](#cell). Used to specify what an HTML page should look like, or a JSON response, or an XML dump, etc.

- found in `src/Template/Layout/*`
- `src/Template/Layout/default.ctp` is the default Layout of an HTML page for your app
- intended to contain (wrap) [Templates](#template)
- can contain [Elements](#element)
- can contain [View Blocks](#view-block)
- can contain [View Cells](#cells)
- active layout can be specified in [Controller](#controller) via `$this->layout = 'blah';`
- is the **last** of the [View](view) elements to be rendered 

##### Template
The most common view file. Typically a single template file for each of a [Controller](#controller)'s [actions](#action).

- Can be passed data from it's [Controller](#controller) using `$this->set('varname', $value)` [[link]](http://book.cakephp.org/3.0/en/controllers.html#setting-view-variables)
- Typically one per [Controller](#controller) [action](#action)
- `src/Template/Ewoks/view.ctp` is (by default) template for the `EwoksController::view` action
- can contain [Elements](#element)
- can manipulate/contain [View Blocks](#view-block)
- can contain [View Cells](#cell)

##### Element
An element is a small (most often re-useable) chunk of view code. Often used to display HTML used by multiple [Layouts](#layout) or [Templates](#template) - e.g. your nav menu.

- found in `src/Template/Element/blah.ctp`
- useable in [Layouts](#layout), [View Blocks](#view-block), [Templates](#template), [Elements](#element), and [Cells](#cell).
- elements can be cached [[link]](http://book.cakephp.org/3.0/en/views.html#caching-elements)
- `$this->element('my-element', ['foo'=>'bar'])` loads `src/Template/Element/my-element.ctp` and creates variable `$foo` with value `'bar'` [[link]](http://book.cakephp.org/3.0/en/views.html#passing-variables-into-an-element)

##### View Block
[[API]](http://api.cakephp.org/3.0/class-Cake.View.ViewBlock.html)
View Blocks use the output buffer to encapsulate chunks of code into a variable that can be easily referenced, manipulated, or checked in other view pieces like [Layouts](#layout), other [View Blocks](#view-block), [Templates](#template), [Elements](#element), and [Cells](#cell).

- anything in-between `$this->start('my-block)` and `$this->end()` will be placed into a view block named `my-block` [[link]](http://book.cakephp.org/3.0/en/views.html#using-view-blocks)
- get the contents of a block using `$this->fetch('my-block')` [[link]](http://book.cakephp.org/3.0/en/views.html#displaying-blocks)
- can be modified inside [Helpers](#helper)
  - e.g. HtmlHelper uses View Blocks to buffer script output [[link]](http://book.cakephp.org/3.0/en/views.html#using-blocks-for-script-and-css-files)

##### Cell
[[API]](http://api.cakephp.org/3.0/class-Cake.View.Cell.html)
View Cells let you talk to a [Model](#model) directly anywhere, in any other [View](#view) file, allowing access to unrelated model data (e.g. show Shipping Options on an Item View) without needing to couple unrelated Models/Controllers together

- main Cell class is like a tiny, custom mini-controller [[link]](http://book.cakephp.org/3.0/en/views/cells.html#creating-a-cell)
- Cell class is singular, suffixed `class EwokCell extends Cell`
- Cell class found in `src/View/Cell/EwokCell.php`
- Cell views (templates) in e.g. `src/Template/Cell/Ewoks/health.ctp` where `health` is a public function in `EwokCell`
- Can be used in any [View](#view) via `$this->cell('Ewok::health', [ .. params .. ])`

##### Helper
The Helper is to the [View](#view) what [Components](#component) are to [Controllers](#controller) and [Behaviors](#behavior) to [Models](#model) - they are a way to encapsulate reusable code for views.

- **Helper Setup**
  - `namespace App\View\Helper`
  - `use Cake\View\Helper`
  - `class TimeHelper extends Helper` suffixed [[link]](http://book.cakephp.org/3.0/en/views/helpers.html#creating-helpers)
  - `src/View/Helper/TimeHelper.php`
- **Helper Use**
  - can be used in Views without suffix e.g. `$this->Time->timeAgoInWords(timestamp)` [[link]](http://book.cakephp.org/3.0/en/views/helpers.html#using-your-helper)
  - can use other Helpers [[link]](http://book.cakephp.org/3.0/en/views/helpers.html#including-other-helpers)
- **Helper Methods**
  - can interact with [View](#view) lifecycle callbacks [[link]](http://book.cakephp.org/3.0/en/views/helpers.html#callbacks)
- can create/manipulate [View Blocks](#block)
- can be used in [Templates](#template)
- can be used in [Elements](#element)
- can be used in [View Cell](#cell)'s [Templates](#templates)
- can use other [Helpers](#helper) [[link]](http://book.cakephp.org/3.0/en/views/helpers.html#including-other-helpers)


## Controller
Determines intent of incoming Requests (e.g. resolving URLs, Forms, AJAX) and builds and formats a reponse (handed off to the [View](#view) layer).

Controllers possibly have the most built in auto-magic. They automatically link to a Model [Table](#table) with the same name, URLs automatically route to their public methods [[link]](http://book.cakephp.org/3.0/en/development/routing.html#routes-configuration), and Request Headers and Data are automatically parsed into `$this->params` [[link]](http://book.cakephp.org/3.0/en/controllers/request-response.html#request-parameters) by the [RequestHandler](http://book.cakephp.org/3.0/en/controllers/request-response.html#request)

***if they contain business logic you're doing it wrong - that goes in the Model layer***

- **Controller Setup**
  - `namespace App\Controller`
  - `use Cake\Controller\AppController`
  - `class EwoksController extends AppController` plural, suffixed
  - `src/Controller/EwoksController.php`
  - `src/Template/Ewoks/..` location of [Template](#template) files
- **Controller Auto-magic**
  - `$this->request->data` automatically captures request data (FORM submission REST GET, POST, etc)
  - `$this->Ewoks` auto-links to [Model](#model) collection (namely, [Table](#table))
  - `http://.../ewoks/view/123` auto-loads `EwoksController::view(123)` [[link]](http://book.cakephp.org/3.0/en/controllers.html#request-flow)
  - `EwoksController::view(..)` auto-renders [Template](#template) `src/Template/Ewoks/view.ctp` [[link]](http://book.cakephp.org/3.0/en/controllers.html#rendering-a-view)
  - `EwoksController::view(..)` auto-renders [Layout](#layout) `src/Template/Layouts/default.ctp` [[link]](http://book.cakephp.org/3.0/en/controllers.html#rendering-a-view)
- **Controller Helper**
  - can use [Components](#component)

##### Component
Components are a way to re-use common code between [Controllers](#controller). Components are to [Controllers](#controller) what [Helpers](#helper) are to [Views](#view) and [Behaviors](#behavior) are to the [Model](#model) layer.

- **Component Setup**
  - `namespace App\Controller\Component`
  - `use Cake\Controller\Component` 
  - `class MathComponent extends Component` singular, suffixed [[link]](http://book.cakephp.org/3.0/en/controllers/components.html#creating-a-component)
  - `src/Controller/Component/MathComponent.php` 
- **Component Usage**
  - used in [Controllers](#controller) `$this->Math->factorial(10)` [[link]](http://book.cakephp.org/3.0/en/controllers/components.html#using-components)
  - can use other [Components](#component) [[link]](http://book.cakephp.org/3.0/en/controllers/components.html#including-your-component-in-your-controllers)
  - `Controller::
- **Component Methods**
  - lifecycle callbacks [[link]](http://book.cakephp.org/3.0/en/controllers/components.html#component-callbacks)