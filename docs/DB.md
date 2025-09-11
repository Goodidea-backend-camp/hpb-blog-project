```dbml
// HPB-Blog 資料庫結構

Table users {
  id integer [primary key, increment]
  username varchar(255) [not null, unique]
  password varchar(255) [not null, note: 'Hashed password']
  remember_token varchar(100) [null]
  created_at timestamp [default: `CURRENT_TIMESTAMP`]
  updated_at timestamp [default: `CURRENT_TIMESTAMP`]
  deleted_at timestamp [null, note: 'Soft delete']

  Note: 'User accounts and basic activity tracking'
}

Table posts {
  id integer [primary key, increment]
  user_id integer [not null]
  title varchar(255) [not null]
  slug varchar(255) [not null, unique, note: 'SEO-friendly URL']
  content text [not null, note: 'Markdown content']
  published_at timestamp [null]
  created_at timestamp [default: `CURRENT_TIMESTAMP`]
  updated_at timestamp [default: `CURRENT_TIMESTAMP`]
  deleted_at timestamp [null, note: 'Soft delete']

  Note: 'Blog posts with Markdown support'
}

// 關聯關係定義
Ref user_posts: posts.user_id > users.id [delete: cascade]
```