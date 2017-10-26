# Goal: Integrating CKEditor in any place in the Sylius form, for example, a product description.

> This is the list of Sylius plugins, tutorials, community access and other Sylius dev-related stuff.

### Installing CKEditor

> You can follow [this official Symfony guide](https://symfony.com/doc/master/bundles/IvoryCKEditorBundle/index.html) or read the below basic step by step setup.
1. Require the bundle: `composer require egeloen/ckeditor-bundle`
2. Add CKEditor bundle to your `AppKernel.php` file:

```php
class AppKernel extends Kernel
{
    public function registerBundles()
    {
        $bundles = array(
            new Ivory\CKEditorBundle\IvoryCKEditorBundle(),
            // ...
        );

        // ...
    }
}
```
2. Install CKEditor asset files `bin/console ckeditor:install`
3. Install assets `bin/console assets:install`
4. Add the CKEditor JS assets files to your layout file, for instance, if you want to enable WYSIWYG editor in the backend, add it to the `app/Resources/SyliusAdminBundle/views/layout.html.twig`:

```twig
...

{% block javascripts %}
    {% include 'SyliusUiBundle::_javascripts.html.twig' with {'path': 'assets/admin/js/app.js'} %}
    {% include 'SyliusUiBundle::_javascripts.html.twig' with {'path': 'assets/admin/theme/js/app.js'} %}

    {{ sonata_block_render_event('sylius.admin.layout.javascripts') }}
{% endblock %}
```

### Adding WYSIWYG to product description field
> We will use the official Symfony form extension method. Read [the official Symfony form extension documentation](https://symfony.com/doc/current/form/create_form_type_extension.html) for more information.

1. Create a ProductTranslationType form extension:

```php
<?php

declare(strict_types=1);

namespace AppBundle\Form\Extension;

use Ivory\CKEditorBundle\Form\Type\CKEditorType;
use Sylius\Bundle\ProductBundle\Form\Type\ProductTranslationType;
use Symfony\Component\Form\AbstractTypeExtension;
use Symfony\Component\Form\FormBuilderInterface;

final class ProductTranslationTypeExtension extends AbstractTypeExtension
{
    /**
     * {@inheritdoc}
     */
    public function buildForm(FormBuilderInterface $builder, array $options): void
    {
        $builder
            ->remove('description')
            ->add('description', CKEditorType::class, [
                'required' => false,
                'label' => 'sylius.form.product.description',
            ])
        ;
    }

    /**
     * {@inheritdoc}
     */
    public function getExtendedType(): string
    {
        return ProductTranslationType::class;
    }
}
```
2. Register it as a service:

```yaml
services:
    admin.form.extension.product_translation:
        class: AppBundle\Form\Extension\ProductTranslationTypeExtension
        tags:
            - { name: form.type_extension, extended_type: Sylius\Bundle\ProductBundle\Form\Type\ProductTranslationType, priority: -1 }
```
3. Open an existing or create new `app/Resources/SyliusShopBundle/views/Product/Show/Tabs/_details.html.twig` and add `|raw` Twig filter to the `{{ product.description }}`. This will allow displaying a parsed HTML output.

```twig
<h2>{{ 'sylius.ui.description'|trans }}</h2>

{% if product.description is not empty %}
    {{ product.description|raw }}
{% else %}
    {{ 'sylius.ui.no_description'|trans }}.
{% endif %}
```