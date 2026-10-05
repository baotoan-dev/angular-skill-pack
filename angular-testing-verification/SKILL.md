---
name: angular-testing-verification
description: >-
  Use this skill when writing unit tests for Angular components/services, executing test suites with Karma/Jasmine or Vitest,
  mocking HTTP endpoints, or debugging test failures.
---

# Angular Component & Service Testing Skill

This skill provides patterns for testing modern Angular components with Signals and standalone dependencies.

## 1. Component Unit Test Pattern (`.component.spec.ts`)

```typescript
import { ComponentFixture, TestBed } from '@angular/core/testing';
import { CustomerCardComponent } from './customer-card.component';
import { ComponentRef } from '@angular/core';

describe('CustomerCardComponent', () => {
  let component: CustomerCardComponent;
  let componentRef: ComponentRef<CustomerCardComponent>;
  let fixture: ComponentFixture<CustomerCardComponent>;

  beforeEach(async () => {
    await TestBed.configureTestingModule({
      imports: [CustomerCardComponent],
    }).compileComponents();

    fixture = TestBed.createComponent(CustomerCardComponent);
    component = fixture.componentInstance;
    componentRef = fixture.componentRef;

    // Set Signal Input
    componentRef.setInput('customer', {
      id: 'cust-1',
      name: 'Acme Corp',
      email: 'contact@acme.com',
      status: 'active',
      value: 50000,
    });

    fixture.detectChanges();
  });

  it('should create component', () => {
    expect(component).toBeTruthy();
  });

  it('should calculate correct status badge color', () => {
    expect(component.statusBadgeColor()).toBe('badge-success');
  });

  it('should emit selectCustomer event when View Details is clicked', () => {
    spyOn(component.selectCustomer, 'emit');
    component.toggleExpand();
    fixture.detectChanges();

    const button = fixture.nativeElement.querySelector('.btn-primary');
    button.click();

    expect(component.selectCustomer.emit).toHaveBeenCalledWith('cust-1');
  });
});
```

## 2. Running Unit Tests in CLI

To run tests headlessly:
```bash
npx ng test --watch=false --browsers=ChromeHeadless
```
