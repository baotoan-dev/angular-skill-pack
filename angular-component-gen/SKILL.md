---
name: angular-component-gen
description: >-
  Use this skill when creating or scaffolding new Angular components, pages, forms,
  services, or UI controls adhering to Angular v17+ standards (standalone components,
  signals, OnPush change detection, new control flow syntax, and premium CSS/Tailwind styling).
---

# Modern Angular Component Generator Skill

This skill provides step-by-step instructions and code patterns for building robust, high-performance Angular components using modern Angular features.

## Step 1: Component Planning
Before writing code, define:
1. **Inputs/Outputs**: Signal inputs (`input<T>()`, `input.required<T>()`) and outputs (`output<T>()`).
2. **State Signals**: Internal reactive state (`signal<T>()`, `computed<T>()`).
3. **Dependencies**: Injected services (`inject(MyService)`).
4. **Layout**: Responsive grid/flex layout with modern CSS/Tailwind styling.

## Step 2: Component TypeScript (`.component.ts`) Pattern

```typescript
import {
  Component,
  ChangeDetectionStrategy,
  input,
  output,
  signal,
  computed,
  inject,
} from '@angular/core';
import { CommonModule } from '@angular/common';

export interface CustomerModel {
  id: string;
  name: string;
  email: string;
  status: 'active' | 'lead' | 'churned';
  value: number;
}

@Component({
  selector: 'app-customer-card',
  standalone: true,
  imports: [CommonModule],
  templateUrl: './customer-card.component.html',
  styleUrl: './customer-card.component.scss',
  changeDetection: ChangeDetectionStrategy.OnPush,
})
export class CustomerCardComponent {
  // Signal Inputs & Outputs
  readonly customer = input.required<CustomerModel>();
  readonly selectCustomer = output<string>();

  // Local Signal State
  readonly isExpanded = signal(false);

  // Computed Values
  readonly statusBadgeColor = computed(() => {
    switch (this.customer().status) {
      case 'active': return 'badge-success';
      case 'lead': return 'badge-warning';
      case 'churned': return 'badge-danger';
    }
  });

  toggleExpand(): void {
    this.isExpanded.update((v) => !v);
  }

  onSelect(): void {
    this.selectCustomer.emit(this.customer().id);
  }
}
```

## Step 3: Template (`.component.html`) Pattern with Modern Control Flow

```html
<div class="customer-card-container" [class.expanded]="isExpanded()">
  <div class="card-header" (click)="toggleExpand()">
    <div class="info">
      <h3>{{ customer().name }}</h3>
      <p class="email">{{ customer().email }}</p>
    </div>
    
    <span class="badge" [ngClass]="statusBadgeColor()">
      {{ customer().status | uppercase }}
    </span>
  </div>

  @if (isExpanded()) {
    <div class="card-details animate-fade-in">
      <p><strong>Customer Value:</strong> {{ customer().value | currency }}</p>

      <div class="actions">
        <button class="btn btn-primary" (click)="onSelect()">
          View Details
        </button>
      </div>
    </div>
  }
</div>
```

## Step 4: Component Scss (`.component.scss`) Pattern

```scss
.customer-card-container {
  background: rgba(255, 255, 255, 0.05);
  backdrop-filter: blur(16px);
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: 16px;
  padding: 1.25rem;
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);

  &:hover {
    transform: translateY(-2px);
    box-shadow: 0 12px 24px -8px rgba(0, 0, 0, 0.3);
    border-color: rgba(99, 102, 241, 0.4);
  }

  .card-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    cursor: pointer;
  }

  .badge {
    padding: 0.25rem 0.75rem;
    border-radius: 9999px;
    font-size: 0.75rem;
    font-weight: 600;

    &-success { background: rgba(16, 185, 129, 0.2); color: #34d399; }
    &-warning { background: rgba(245, 158, 11, 0.2); color: #fbbf24; }
    &-danger { background: rgba(239, 68, 68, 0.2); color: #f87171; }
  }
}
```

## Verification Checklist
- [ ] Uses `standalone: true`
- [ ] Uses `ChangeDetectionStrategy.OnPush`
- [ ] Uses `inject()` for dependency injection
- [ ] Uses `@if` / `@for` with `track` instead of `*ngIf` / `*ngFor`
- [ ] Component compiles cleanly without Angular CLI warnings
