---
layout: post
title:  "Rails中重置PostgreSQL 自增主键的值"
date:   2016-06-11 23:47
tags: rails
---

# 重置单个表 在rails console中执行以下代码：

{% codeblock lang:ruby %}
table = 'my_table'
  auto_inc_val = 10    # New auto increment start point
  ActiveRecord::Base.connection.execute(
  "ALTER SEQUENCE #{table}_id_seq RESTART WITH #{auto_inc_val}"
)
{% endcodeblock %}

# 重置所有表 在rails console中执行以下代码：

{% codeblock lang:ruby %}
ActiveRecord::Base.connection.tables.each do |table|
    result = ActiveRecord::Base.connection.execute("SELECT id FROM #{table} ORDER BY id DESC LIMIT 1")
    if result.any?
      ai_val = result.first['id'].to_i + 1
        puts "Resetting auto increment ID for #{table} to #{ai_val}"
        ActiveRecord::Base.connection.execute("ALTER SEQUENCE #{table}_id_seq RESTART WITH #{ai_val}")
    end
  end
{% endcodeblock %}
