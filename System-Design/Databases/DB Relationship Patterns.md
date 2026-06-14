---
id: 948fc0b3-bf06-4d3f-8c70-1ded217bc422
title: DB Relationship Patterns
language: markdown
tags: [system-design, databases, database-design, relationships]
selection: null
isPinned: false
timestamp: 1780926643992
---

**Links**: [[Data Normalization Rules]] | [[SQL JOIN Operations]] | [[Database Indexing Deep Dive]] | [[Database Triggers]] | [[SQL Query Optimization]] | [[Database Views]]


# DB Relationship Patterns

Relationships between tables are the heart of relational databases.

## One-to-One (1:1)

```sql
CREATE TABLE user (
    id SERIAL PRIMARY KEY,
    username TEXT NOT NULL UNIQUE
);

CREATE TABLE profile (
    user_id INTEGER PRIMARY KEY REFERENCES user(id),
    bio TEXT,
    avatar_url TEXT
);
```

## One-to-Many (1:N)

```sql
CREATE TABLE department (
    id SERIAL PRIMARY KEY,
    name TEXT NOT NULL
);

CREATE TABLE employee (
    id SERIAL PRIMARY KEY,
    name TEXT NOT NULL,
    dept_id INTEGER REFERENCES department(id)
);
```

## Many-to-Many (M:N)

```sql
CREATE TABLE student (
    id SERIAL PRIMARY KEY,
    name TEXT NOT NULL
);

CREATE TABLE course (
    id SERIAL PRIMARY KEY,
    title TEXT NOT NULL
);

CREATE TABLE enrollment (
    student_id INTEGER REFERENCES student(id),
    course_id INTEGER REFERENCES course(id),
    enrolled_at TIMESTAMP DEFAULT NOW(),
    PRIMARY KEY (student_id, course_id)
);
```

## Referential Actions

| Action | Effect |
|--------|--------|
| CASCADE | Delete/update child rows |
| SET NULL | Set child FK to NULL |
| RESTRICT | Prevent deletion |
| NO ACTION | Same as RESTRICT (deferred) |

**See also**: [[Data Normalization Rules]], [[SQL JOIN Operations]], [[Database Engines Compared]], [[PostgreSQL Features]]
