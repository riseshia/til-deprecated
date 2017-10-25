# Eager load path

eager_load_path는 autoload 대상.

그러니까 autoload하고 싶으면 그냥 eager_load_path에 추가하면 됨.

```
config.eager_load_paths << Rails.root.join('lib/autoload')
```

## 참고

https://github.com/rails/rails/blob/e4654aa93c3cf21949b72873072833b766ef7770/railties/lib/rails/engine.rb#L684
https://github.com/rails/rails/blob/e4654aa93c3cf21949b72873072833b766ef7770/railties/lib/rails/engine.rb#L566
