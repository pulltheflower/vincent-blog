---
layout: post
title:  "Ruby 我的最爱"
date:   2021-09-15 17:02:31 +0800
categories: jekyll update
---


### ActiveSupport 的实现原理

在ruby的开发当中我们经常会在concerns目录的module当中看到这样神奇的一行

```ruby
extend ActiveSupport::Concern
```

有了这一行，我们就可以使用一些很酷的方法，例如`included do ... end` `prepended do ... end` `class_methods do ... end`这样的方法，来定义当这个module被引入时，对引入这个module的类的操作，那么这个 `ActiveSupport::Concern` 究竟做了什么神奇的操作呢，让我们研究一下。

我们都知道如何在一个类中引入一个module，例如有两个类`Person`、`Tiger`

```ruby
class Person
  attr_accessor :name, :words
  def initialize(name, words)
    self.name = name
    self.words = words
  end
end


class Tiger
  attr_accessor :name, :words
  def initialize(name, words)
    self.name = name
    self.words = words
  end
end
```

我们希望给`Person` 类和 `Tiger` 类都添加一个`say`方法，让他们发出自己的声音，这个时候我们可以使用混入的方式，在同一个module中定义这个方法，并在类中混入这个方法，我们定义一个 `Behavior` module

```ruby
module Behavior
  def say
    puts "#{self.name} says #{self.words}"
  end
end
```

然后我们类中 `include` 这个module

```ruby
class Person
  include ::Behavior
  attr_accessor :name, :words
  def initialize(name, words)
    self.name = name
    self.words = words
  end
end


class Tiger
  include ::Behavior
  attr_accessor :name, :words
  def initialize(name, words)
    self.name = name
    self.words = words
  end
end
```

然后执行`Tiger.new("jack", "ahhhhhhhh").say` 系统会输出 `jack says ahhhhhhhh` 我们混入的方法生效了，`include` 方法让我们成功将`Behavior` module中的say方法添加到了 `Person` 和 `Tiger` 两个类中成为了他们的实例方法

但是当我们想要给 `Person` 和 `Tiger` 都添加一个类方法叫做 `laugh` 的时候，我们该怎么做呢，有人可能会想到用 `extend` 的方式引入一个module，这样module中的方法会变为`extend` 这个module 的类的类方法。但是如果我们想把 `Person` 和 `Tiger` 的行为都在`Behavior` 这个类中定义呢，要怎么实现，那我们可以使用 `self.included` 这个回调函数，这个函数会在一个module 被 `include`的时候调用

```ruby
module Behavior
  def say
    puts "#{self.name} says #{self.words}"
  end

  def self.included(base)
    def self.laugh
      puts "^_^"
    end
  end
end
```

这个时候我们执行 `Tiger.laugh` 或者 `Person.laugh` 都会输出 `^_^` 我们成功将 `laugh` 方法当成了类方法加入到了 `Person` 和 `Tiger` 类中而并没有使用 `extend` 方法