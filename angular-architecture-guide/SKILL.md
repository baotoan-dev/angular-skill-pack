---
name: angular-architecture-guide
description: >-
  Use this skill when designing application architecture, feature modules, state stores,
  HTTP services, authentication flows, route guards, or interceptors for Angular CRM applications.
---

# Angular CRM Architecture & State Management Guide

This skill provides architectural guidance for structuring scalable, maintainable Angular CRM applications.

## 1. State Management: Signal Store Pattern
Use lightweight Signal Stores for managing feature-level state cleanly without excessive boilerplate.

```typescript
import { Injectable, signal, computed, inject } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { tap, catchError } from 'rxjs/operators';
import { of } from 'rxjs';

export interface Lead {
  id: string;
  company: string;
  contactName: string;
  email: string;
  dealValue: number;
  stage: 'new' | 'contacted' | 'proposal' | 'won' | 'lost';
}

export interface LeadsState {
  leads: Lead[];
  loading: boolean;
  filter: string;
  error: string | null;
}

@Injectable({ providedIn: 'root' })
export class LeadsStore {
  private readonly http = inject(HttpClient);

  // Private state signals
  private readonly state = signal<LeadsState>({
    leads: [],
    loading: false,
    filter: '',
    error: null,
  });

  // Public selectors (computed)
  readonly leads = computed(() => {
    const filter = this.state().filter.toLowerCase();
    return this.state().leads.filter(l => 
      l.company.toLowerCase().includes(filter) || 
      l.contactName.toLowerCase().includes(filter)
    );
  });
  readonly loading = computed(() => this.state().loading);
  readonly totalPipelineValue = computed(() => 
    this.state().leads.reduce((sum, item) => sum + item.dealValue, 0)
  );

  // Actions
  setFilter(filter: string): void {
    this.state.update((s) => ({ ...s, filter }));
  }

  loadLeads(): void {
    this.state.update((s) => ({ ...s, loading: true, error: null }));
    this.http.get<Lead[]>('/api/leads').pipe(
      tap((leads) => {
        this.state.update((s) => ({ ...s, leads, loading: false }));
      }),
      catchError((err) => {
        this.state.update((s) => ({ ...s, loading: false, error: err.message }));
        return of([]);
      })
    ).subscribe();
  }
}
```

## 2. Standalone Routing & Lazy Loading Strategy

Define clean, lazy-loaded routes in `app.routes.ts`:

```typescript
import { Routes } from '@angular/router';

export const routes: Routes = [
  {
    path: '',
    redirectTo: 'dashboard',
    pathMatch: 'full',
  },
  {
    path: 'dashboard',
    loadComponent: () =>
      import('./features/dashboard/dashboard.component').then(m => m.DashboardComponent),
    title: 'CRM Dashboard',
  },
  {
    path: 'leads',
    loadComponent: () =>
      import('./features/leads/leads-list.component').then(m => m.LeadsListComponent),
    title: 'Leads Management',
  },
  {
    path: 'customers',
    loadComponent: () =>
      import('./features/customers/customer-list.component').then(m => m.CustomerListComponent),
    title: 'Customer Directory',
  },
  {
    path: '**',
    redirectTo: 'dashboard',
  },
];
```

## 3. HTTP Interceptors (Functional API)

Use functional interceptors for Auth headers and error logging:

```typescript
import { HttpInterceptorFn } from '@angular/common/http';
import { inject } from '@angular/core';
import { AuthService } from '../services/auth.service';

export const authInterceptor: HttpInterceptorFn = (req, next) => {
  const authService = inject(AuthService);
  const token = authService.getToken();

  if (token) {
    const authReq = req.clone({
      headers: req.headers.set('Authorization', `Bearer ${token}`),
    });
    return next(authReq);
  }

  return next(req);
};
```
