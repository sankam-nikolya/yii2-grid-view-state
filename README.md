# yii2-grid-view-state
Save filters from GridView to session, keep the filter state between pages.

## Features
1. Very flexible. Separate setting and getting.
2. Setting via behavior.
3. Determines uniqueness by action's route and a customizable ID.

## Installation

To install, either run

```
php composer.phar require --prefer-dist thrieu/yii2-grid-view-state "dev-master"
```
    
or add
    
```
"thrieu/yii2-grid-view-state": "dev-master"
```
    
to the require section of your `composer.json` file and then run `composer update`.

## Usage
### Step 1
Create \app\widgets\Gridview.php

Extend `GridView` class, simply implement `FilterStateInterface` and `FilterStateTrait`.
```php
namespace \app\widgets;

use thrieu\grid\FilterStateInterface;
use thrieu\grid\FilterStateTrait;

class GridView extends \yii\grid\GridView implements FilterStateInterface {
    use FilterStateTrait;
}
```
### Step 2
Attach the filter behavior to your `GridView` widget.
```php
use \app\widgets\Gridview;
...

GridView::widget([
...
    'as filterBehavior' => \thrieu\grid\FilterStateBehavior::className(),
...
]);
```
### Step 3
Update \app\models\xxxSearch.php

To get the params which is merged with GridView state params and GET query params, and then set it to filter model and `DataProvider`.
```php
// DataProvider
$dataProvider = new ActiveDataProvider([
    'query' => $query,
    'pagination' => [
        'params' => \app\widgets\GridView::getMergedFilterStateParams(),
    ],
    'sort' => [
        'params' => \app\widgets\GridView::getMergedFilterStateParams(),
    ],
]);
// Filter model
$this->load(\app\widgets\GridView::getMergedFilterStateParams());

```

Enjoy it.

### Clear state
Update \app\controllers\xxxController.php

Add `ClearFilterStateBehavior` to `behaviors()` of your controller.
```php
    public function behaviors()
    {
        return [
            ...
            'clearFilterState' => \thrieu\grid\ClearFilterStateBehavior::className(),
            ...
        ];
    }

```
And then add a form to your frontend page.
```php
        $form = Html::beginForm();
        $form .= Html::hiddenInput('clear-state', '1');
        $form .= Html::hiddenInput('redirect-to', '');
        $form .= Button::widget([
            'label' => Yii::t('app', 'Reset filter'),
        ]);
        $form .= Html::endForm();
        echo $form;
```
