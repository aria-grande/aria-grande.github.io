---
layout: post
title:  Activerecord-import with Sidekiq
author: Aria
category: "Ruby on Rails"
---

background에서 ActiveRecord 인스턴스를 대량 생성하기 위해 [activerecord-import](https://github.com/zdennis/activerecord-import) gem과 [sidekiq](https://github.com/mperham/sidekiq)을 사용하고 있다.
activerecord-import를 사용하면 activerecord의 counter_cache 기능을 사용할 수 없다. 하여,  counter_cache 기능을 직접 구현하기 위해 아래와 같은 로직으로 구성했다.

1. 인스턴스 bulk import!
{% highlight ruby %}
CouponInstance.import!(attributes)
{% endhighlight %}
2. ensure block 내에서 인스턴스 카운트 집계 및 `instance_count` attribute를 업데이트 한다.
{% highlight ruby %}
 # ...
ensure
  count = CouponInstance.where(condition).count
  CouponTemplate.update_attributes!(instance_count: count)
end
{% endhighlight %}

생성하고자 하는 인스턴스 수가 10개 이상이면 sidekiq의 worker를 통해 생성하고, 10개 미만일 경우 어플리케이션 서버에서 바로 생성한다.

## The Problem
`instance_count` 숫자가 맞지 않는다.

2000건을 발행하면 **발행량/발행요청량** 이 2000/2000으로 되어야 하는데, **0/2000** 으로 업데이트 된다.

로그를 찍어보니, 한 번 씩 밀린다.

- 00:00 100건 발행 요청 -> 0/100
- 01:00 100건 발행 요청 -> 100/200
- 02:00 300건 발행 요청 -> 200/500

### ensure block의 순서가 보장되지 않는걸까?
- 그런 것 같다.
- 로그를 찍어보니, 2->1 순서로 실행되고 있다.

### sidekiq이 문제인걸까?
- 03:00 5건 발행 요청(worker를 통하지 않음) -> 505/505

worker를 통하지 않으니 실제 인스턴스 갯수 값으로 바로 업데이트 된다.
