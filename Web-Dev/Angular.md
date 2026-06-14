---
id: a1b2c3d4-1194-4000-8000-000000000194
title: Angular
language: markdown
tags: [web-dev, angular, frontend]
selection: null
isPinned: false
timestamp: 1781500001194
---

**Links**: [[React]] | [[Vue.js]] | [[Svelte]] | [[Next.js and Modern Web Frameworks]] | [[State Management Patterns]] | [[Web Components]]


# Angular

Angular is a TypeScript-based web application framework by Google. It provides a complete solution including routing, state management, forms, and HTTP client.

## Architecture

```
Component
    │
    ├── Template (HTML + directives)
    ├── Class (TypeScript logic)
    └── Styles (CSS/SCSS)

Services (injectable, singleton)
    │
    └── Dependency Injection

Modules (NgModule) — organize related code
Router — navigation
HttpClient — API calls
Forms — template-driven or reactive
```

## Component

```typescript
import { Component, Input, Output, EventEmitter, OnInit } from '@angular/core';

@Component({
    selector: 'app-user-card',
    templateUrl: './user-card.component.html',
    styleUrls: ['./user-card.component.scss'],
    changeDetection: ChangeDetectionStrategy.OnPush
})
export class UserCardComponent implements OnInit {
    @Input() user: User;
    @Input() showDetails = false;
    @Output() userClick = new EventEmitter<User>();

    constructor(private analytics: AnalyticsService) {}

    ngOnInit() {
        this.analytics.track('user_card_loaded', { userId: this.user.id });
    }

    handleClick() {
        this.userClick.emit(this.user);
    }
}
```

```html
<!-- user-card.component.html -->
<div class="card" (click)="handleClick()">
    <img [src]="user.avatar" [alt]="user.name" />
    <h3>{{ user.name }}</h3>
    <p *ngIf="showDetails">{{ user.email }}</p>
    <p *ngIf="user.isActive; else inactive">Active</p>
    <ng-template #inactive>Inactive</ng-template>
</div>
```

## Services and Dependency Injection

```typescript
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable, catchError, retry, tap } from 'rxjs';

@Injectable({ providedIn: 'root' })
export class UserService {
    private apiUrl = '/api/users';

    constructor(private http: HttpClient) {}

    getUsers(): Observable<User[]> {
        return this.http.get<User[]>(this.apiUrl).pipe(
            retry(3),
            tap(users => console.log(`Fetched ${users.length} users`)),
            catchError(this.handleError)
        );
    }

    getUser(id: number): Observable<User> {
        return this.http.get<User>(`${this.apiUrl}/${id}`);
    }

    createUser(user: Partial<User>): Observable<User> {
        return this.http.post<User>(this.apiUrl, user);
    }

    private handleError(error: HttpErrorResponse) {
        console.error('API Error:', error);
        return throwError(() => new Error('Something went wrong'));
    }
}
```

## Reactive Forms

```typescript
import { Component } from '@angular/core';
import { FormBuilder, FormGroup, Validators } from '@angular/forms';

@Component({
    selector: 'app-user-form',
    template: `
        <form [formGroup]="userForm" (ngSubmit)="onSubmit()">
            <input formControlName="name" placeholder="Name" />
            <div *ngIf="userForm.get('name').invalid && userForm.get('name').touched">
                Name is required
            </div>

            <input formControlName="email" type="email" placeholder="Email" />
            <div *ngIf="userForm.get('email').errors?.email">
                Invalid email format
            </div>

            <select formControlName="role">
                <option value="admin">Admin</option>
                <option value="user">User</option>
            </select>

            <button type="submit" [disabled]="userForm.invalid">Save</button>
        </form>
    `
})
export class UserFormComponent {
    userForm: FormGroup;

    constructor(private fb: FormBuilder) {
        this.userForm = this.fb.group({
            name: ['', Validators.required],
            email: ['', [Validators.required, Validators.email]],
            role: ['user', Validators.required],
            address: this.fb.group({
                street: [''],
                city: [''],
                zip: ['', Validators.pattern(/^\d{5}$/)]
            })
        });
    }

    onSubmit() {
        if (this.userForm.valid) {
            console.log(this.userForm.value);
        }
    }
}
```

## Routing

```typescript
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { AuthGuard } from './guards/auth.guard';

const routes: Routes = [
    { path: '', component: HomeComponent },
    {
        path: 'users',
        canActivate: [AuthGuard],
        children: [
            { path: '', component: UserListComponent },
            { path: ':id', component: UserDetailComponent, resolve: { user: UserResolver } },
        ]
    },
    { path: 'login', component: LoginComponent },
    { path: '**', component: NotFoundComponent }
];

@NgModule({
    imports: [RouterModule.forRoot(routes)],
    exports: [RouterModule]
})
export class AppRoutingModule {}
```

## Standalone Components (Angular 14+)

```typescript
import { Component } from '@angular/core';
import { CommonModule } from '@angular/common';
import { RouterModule } from '@angular/router';

@Component({
    selector: 'app-home',
    standalone: true,
    imports: [CommonModule, RouterModule],
    template: `
        <h1>Welcome</h1>
        <a routerLink="/users">Users</a>
    `
})
export class HomeComponent {}
```

## Signals (Angular 16+)

```typescript
import { Component, signal, computed, effect } from '@angular/core';

@Component({
    selector: 'app-counter',
    template: `
        <p>Count: {{ count() }}</p>
        <p>Doubled: {{ doubled() }}</p>
        <button (click)="increment()">+1</button>
    `
})
export class CounterComponent {
    count = signal(0);
    doubled = computed(() => this.count() * 2);

    constructor() {
        effect(() => {
            console.log(`Count changed to: ${this.count()}`);
        });
    }

    increment() {
        this.count.update(c => c + 1);
    }
}
```

## Pipes

```typescript
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({ name: 'truncate' })
export class TruncatePipe implements PipeTransform {
    transform(value: string, maxLength: number = 50, ellipsis: string = '...'): string {
        if (!value) return '';
        if (value.length <= maxLength) return value;
        return value.substring(0, maxLength) + ellipsis;
    }
}

// Usage: {{ longText | truncate:30 }}
```
