---
layout: single
title: " [Django] Shopping Mall (7)"
categories: Django_ShoppingMall
tags:
  - Python
  - Django
toc: true
toc_sticky: true
author_profile: false
sidebar:
---
# Variation Manager

{% raw %}

## 모델만들기

models.py
```python
class VariationManager(models.Manager):

    def colors(self):
        return super(VariationManager, self).filter(variation_category='color', is_active=True)

    def sizes(self):
        return super(VariationManager, self).filter(variation_category='size', is_active=True)

variation_category_choice = (
    ('color', 'color'),
    ('size', 'size'),
)

class Variation(models.Model):
    product = models.ForeignKey(Product, on_delete=models.CASCADE)
    variation_category = models.CharField(max_length=100, choices=variation_category_choice)
    variation_value     = models.CharField(max_length=100)
    is_active           = models.BooleanField(default=True)
    created_date        = models.DateTimeField(auto_now=True)

    # objects = VariationManager()

    def __unicode__(self):
        return self.product
```
>- [choices](https://yeko90.tistory.com/entry/%EC%9E%A5%EA%B3%A0-%EA%B8%B0%EC%B4%88-choices%EB%A5%BC-%ED%86%B5%ED%95%9C-%EB%93%9C%EB%A1%AD-%EB%8B%A4%EC%9A%B4-%EB%A9%94%EB%89%B4-%EB%A7%8C%EB%93%A4%EA%B8%B0)
>- 오류 해결 방법에는 여러가지가 있는데 str로 감싸주나 `__unicode__` 로 사용해주면 된다.
>- [__str__오류](https://comdoc.tistory.com/entry/7-Django-ORM-Method-str)
>- ![](https://i.imgur.com/OagohOM.png)




## 어드민 등록하기
#### admin.py
```python
class VariationAdmin(admin.ModelAdmin):

    list_display = ('product', 'variation_category', 'variation_value', 'is_active')
    list_editable = ('is_active',)
    list_filter = ('product', 'variation_category', 'variation_value')

admin.site.register(Product, ProductAdmin)
admin.site.register(Variation, VariationAdmin)
```
>- 상품이 품절 되었을 경우를 위해 is_active 부분을 컨트롤 할 수 있게 수정해줬다.

![](https://i.imgur.com/mnfjsQX.png)


## 템플릿 연결시키기

```python
<div class="row">
<div class="item-option-select">
<h6>Choose Color</h6>
	<select name="color" class="form-control" required="required">
		<option value="" disabled="disabled" selected="selected">Select</option>
		{% for i in single_product.variation_set.colors %}
		<option value="{{ i.variation_value | lower }}">{{ i.variation_value | capfirst }}</option>
		{% endfor %}
	</select>
	</div>
	</div>
	<div class="row">
	<div class="item-option-select">
	<h6>Select Size</h6>
		<select name="size" class="form-control" required="required">
		<option value="" disabled="disabled" selected="selected">Select</option>
	{% for i in single_product.variation_set.sizes %}
		<option value="{{ i.variation_value | lower }}">{{ i.variation_value | capfirst }}</option>
	{% endfor %}
	</select>
</div>
</div>
```
>- [`variation_set`](https://fenderist.tistory.com/368) 외래키로 등록된 필드는 `_set` 을 사용하면 사용할 수 있다.
>- ForeignKey 에서 매개변수 related_name 를 등록하면 그 이름으로 호출할 수 있다. 물론 `_set_`으로는 부를 수 없다.
>- 외래키에서 호출 할 수 있도록 color 와 size를 모델에서 등록해주자.


#### models.py
```python
class VariationManager(models.Manager):
    def colors(self):
        return super(VariationManager, self).filter(variation_category='color', is_active=True)
    def sizes(self):
        return super(VariationManager, self).filter(variation_category='size', is_active=True)
```
>- html 템플릿에서 반복문의 인자로 등록해준다.
>- `variation_set.sizes` 는 모델에 정의된 `class VariationManager` 에서 정의된 `sizes` 함수를 호출한다.




{% endraw %}
