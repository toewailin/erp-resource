
### Step 1: Initialize the Foundation (Laravel + React/Inertia)

To use `shadcn/ui` effectively, you need a React + TypeScript environment. Laravel Breeze provides the perfect starting point with Inertia.js.

```bash
# 1. Create a fresh Laravel project
laravel new erp-system

# Choose these options when prompted:
# - Starter Kit: Laravel Breeze
# - Frontend: React with Inertia
# - Testing Framework: Pest
# - Database: MySQL or PostgreSQL

cd erp-system

# 2. Install Node dependencies
npm install

```

### Step 2: Install UI Foundations (`shadcn/ui`)

Since `shadcn/ui` is a collection of reusable components rather than a typical npm package, you need to initialize it in your React environment.

```bash
# 1. Initialize shadcn/ui in your project
npx shadcn-ui@latest init

# Choose these options during init:
# - TypeScript: Yes
# - Style: Default or New York (New York is great for dense ERP dashboards)
# - Base color: Slate or Zinc
# - Global CSS file: resources/css/app.css
# - Tailwind config: tailwind.config.js
# - Components path: resources/js/Components/ui
# - Utils path: resources/js/lib/utils

# 2. Install your first essential ERP components (Buttons, Tables, Forms)
npx shadcn-ui@latest add button table input card dialog

```

### Step 3: Establish the Modular Core (`laravel-modules`)

Now, transform the standard Laravel monolith into a modular architecture.

```bash
# 1. Install the package
composer require nwidart/laravel-modules

# 2. Publish the configuration
php artisan vendor:publish --provider="Nwidart\Modules\LaravelModulesServiceProvider"

# 3. Autoload the modules in your composer.json
# Add this inside the "autoload": { "psr-4": { ... } } block:
# "Modules\\": "Modules/"

# 4. Dump autoload to register the new namespace
composer dump-autoload

# 5. Create your first module (e.g., HR)
php artisan module:make HR

```

*Your application will now have a `Modules/HR` directory containing its own Routes, Controllers, and Models.*

### Step 4: Data Tracking & Integrity (`activitylog` + `SoftDeletes`)

Configure the database to track every change and prevent permanent data loss.

```bash
# 1. Install Spatie Activitylog
composer require spatie/laravel-activitylog

# 2. Publish and run the migrations for the activity log table
php artisan vendor:publish --provider="Spatie\Activitylog\ActivitylogServiceProvider" --tag="activitylog-migrations"
php artisan migrate

```

### Step 5: Tie It Together (The Model Blueprint)

Here is how you apply these concepts inside your newly created `HR` module. Let's create an `Employee` model.

**1. Create the Model and Migration:**

```bash
php artisan module:make-model Employee HR -m

```

**2. Update the Migration (`Modules/HR/Database/Migrations/xxx_create_employees_table.php`):**

```php
public function up()
{
    Schema::create('hr_employees', function (Blueprint $table) {
        $table->id();
        $table->string('name');
        $table->string('email')->unique();
        $table->string('position');
        $table->timestamps();
        $table->softDeletes(); // <-- Crucial for ERP
    });
}

```

**3. Update the Model (`Modules/HR/Entities/Employee.php`):**

```php
namespace Modules\HR\Entities;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\SoftDeletes;
use Spatie\Activitylog\Traits\LogsActivity;
use Spatie\Activitylog\LogOptions;

class Employee extends Model
{
    use SoftDeletes, LogsActivity;

    protected $table = 'hr_employees';
    protected $fillable = ['name', 'email', 'position'];

    // Configure the Activity Log
    public function getActivitylogOptions(): LogOptions
    {
        return LogOptions::defaults()
            ->logFillable() // Logs changes to any fillable attributes
            ->logOnlyDirty() // Only logs what actually changed
            ->dontSubmitEmptyLogs()
            ->useLogName('hr_module'); 
    }
}

```

### Step 6: The Frontend Bridge (Inertia + Modules)

When returning views from a Module Controller, you will pass the data via Inertia to your React pages.

```php
// Modules/HR/Http/Controllers/EmployeeController.php

namespace Modules\HR\Http\Controllers;

use App\Http\Controllers\Controller;
use Modules\HR\Entities\Employee;
use Inertia\Inertia;

class EmployeeController extends Controller
{
    public function index()
    {
        // Fetch employees without the deleted ones
        $employees = Employee::latest()->paginate(10);
        
        // Return an Inertia React component 
        // Ensure you create resources/js/Pages/HR/Employees/Index.tsx
        return Inertia::render('HR/Employees/Index', [
            'employees' => $employees
        ]);
    }
}

```

---

With this setup, you have isolated backend logic, strict activity logging, soft deletes for safety, and a modern React/Tailwind frontend ready for complex tables and forms.

**Which module would you like to build out first to act as the primary blueprint for the rest of the system (e.g., HR, Inventory, or CRM)?**

---

Excellent choice. Starting with the **HR (Human Resources)** module is the perfect way to establish your core patterns. Employee data is relatively straightforward but touches all the critical architectural pieces: data tables, complex forms, unique validations, and activity tracking.

Here is the professional blueprint for building your HR module, step-by-step.

---

### Step 1: The Database Schema (Migration)

An ERP requires strict data definitions. We need a robust `hr_employees` table.

Run this command to create the migration (if you haven't already):

```bash
php artisan module:make-migration create_hr_employees_table HR

```

Open the generated migration file in `Modules/HR/Database/Migrations/` and define the schema:

```php
use Illuminate\Support\Facades\Schema;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Database\Migrations\Migration;

return new class extends Migration
{
    public function up()
    {
        Schema::create('hr_employees', function (Blueprint $table) {
            $table->id();
            $table->string('employee_code')->unique(); // E.g., EMP-001
            $table->string('first_name');
            $table->string('last_name');
            $table->string('email')->unique();
            $table->string('phone')->nullable();
            $table->string('department');
            $table->string('position');
            $table->date('hire_date');
            $table->enum('status', ['active', 'on_leave', 'terminated'])->default('active');
            
            $table->timestamps();
            $table->softDeletes(); // Crucial: Never permanently delete employees
        });
    }

    public function down()
    {
        Schema::dropIfExists('hr_employees');
    }
};

```

Run the migration: `php artisan module:migrate HR`

### Step 2: The Model & Activity Logging

Next, configure the `Employee` model to automatically track every change made to these records.

Open `Modules/HR/Entities/Employee.php`:

```php
namespace Modules\HR\Entities;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\SoftDeletes;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use Spatie\Activitylog\Traits\LogsActivity;
use Spatie\Activitylog\LogOptions;

class Employee extends Model
{
    use HasFactory, SoftDeletes, LogsActivity;

    protected $table = 'hr_employees';

    protected $fillable = [
        'employee_code', 'first_name', 'last_name', 'email', 
        'phone', 'department', 'position', 'hire_date', 'status'
    ];

    // Spatie Activity Log Configuration
    public function getActivitylogOptions(): LogOptions
    {
        return LogOptions::defaults()
            ->logFillable()
            ->logOnlyDirty() // Only logs columns that actually changed
            ->dontSubmitEmptyLogs()
            ->useLogName('hr_employee_updates'); 
    }
}

```

### Step 3: Strict Validation (Form Requests)

In a professional codebase, we **never** validate data inside the Controller. We use Form Requests to keep controllers clean and separate business logic.

```bash
php artisan module:make-request StoreEmployeeRequest HR

```

Open `Modules/HR/Http/Requests/StoreEmployeeRequest.php`:

```php
namespace Modules\HR\Http\Requests;

use Illuminate\Foundation\Http\FormRequest;

class StoreEmployeeRequest extends FormRequest
{
    public function rules()
    {
        return [
            'employee_code' => 'required|string|unique:hr_employees,employee_code',
            'first_name'    => 'required|string|max:255',
            'last_name'     => 'required|string|max:255',
            'email'         => 'required|email|unique:hr_employees,email',
            'phone'         => 'nullable|string|max:20',
            'department'    => 'required|string|max:255',
            'position'      => 'required|string|max:255',
            'hire_date'     => 'required|date',
            'status'        => 'required|in:active,on_leave,terminated',
        ];
    }

    public function authorize()
    {
        return true; // We will handle authorization via middleware/Roles later
    }
}

```

### Step 4: The Controller (Inertia Bridge)

Now, tie the backend to the frontend using the Controller.

Open `Modules/HR/Http/Controllers/EmployeeController.php`:

```php
namespace Modules\HR\Http\Controllers;

use Illuminate\Routing\Controller;
use Modules\HR\Entities\Employee;
use Modules\HR\Http\Requests\StoreEmployeeRequest;
use Inertia\Inertia;

class EmployeeController extends Controller
{
    public function index()
    {
        // Fetch paginated employees
        $employees = Employee::latest()->paginate(10);
        
        return Inertia::render('HR/Employees/Index', [
            'employees' => $employees
        ]);
    }

    public function create()
    {
        return Inertia::render('HR/Employees/Create');
    }

    public function store(StoreEmployeeRequest $request)
    {
        Employee::create($request->validated());

        // Redirect back to index with a success message
        return redirect()->route('hr.employees.index')
            ->with('success', 'Employee created successfully.');
    }
}

```

### Step 5: The Frontend React Interface (`shadcn/ui`)

Finally, let's look at how this maps to your React files. You will create a file at `resources/js/Pages/HR/Employees/Index.tsx`.

Here is a structural example utilizing `shadcn/ui` components for a clean, professional look:

```tsx
import React from 'react';
import { Head, Link } from '@inertiajs/react';
import { Button } from '@/Components/ui/button';
import {
    Table,
    TableBody,
    TableCell,
    TableHead,
    TableHeader,
    TableRow,
} from "@/Components/ui/table";
import { Badge } from "@/Components/ui/badge";

export default function EmployeeIndex({ employees }) {
    return (
        <div className="p-6">
            <Head title="HR - Employees" />
            
            <div className="flex justify-between items-center mb-6">
                <h1 className="text-2xl font-bold tracking-tight">Employee Directory</h1>
                <Link href={route('hr.employees.create')}>
                    <Button>+ Add Employee</Button>
                </Link>
            </div>

            <div className="border rounded-md">
                <Table>
                    <TableHeader>
                        <TableRow>
                            <TableHead>Code</TableHead>
                            <TableHead>Name</TableHead>
                            <TableHead>Department</TableHead>
                            <TableHead>Status</TableHead>
                            <TableHead className="text-right">Actions</TableHead>
                        </TableRow>
                    </TableHeader>
                    <TableBody>
                        {employees.data.map((employee) => (
                            <TableRow key={employee.id}>
                                <TableCell className="font-medium">{employee.employee_code}</TableCell>
                                <TableCell>{employee.first_name} {employee.last_name}</TableCell>
                                <TableCell>{employee.department}</TableCell>
                                <TableCell>
                                    <Badge variant={employee.status === 'active' ? 'default' : 'secondary'}>
                                        {employee.status}
                                    </Badge>
                                </TableCell>
                                <TableCell className="text-right">
                                    <Link href={route('hr.employees.edit', employee.id)} className="text-blue-600 hover:underline mr-4">
                                        Edit
                                    </Link>
                                    {/* Delete action goes here */}
                                </TableCell>
                            </TableRow>
                        ))}
                    </TableBody>
                </Table>
            </div>
            
            {/* Pagination controls would go here */}
        </div>
    );
}

```

---

By following this exact pattern, you have just created a highly secure, logged, and scalable foundation. Every time you add a new module (like Inventory or Accounting), you will replicate this exact workflow.

**Would you like me to guide you through setting up the frontend `useForm` hook for the "Create Employee" page next, or should we set up the Role/Permissions system (`spatie/laravel-permission`) to restrict who can see this HR dashboard?**

---

Let's complete the HR module's creation flow. To ensure PixelVite delivers a highly responsive and polished ERP experience, we will handle the frontend state using Inertia's `useForm` hook. Structuring this submission component—much like an `AddDebtForm.tsx` or similar React form module you might have built previously—allows us to effortlessly bind Laravel's backend validation directly to the `shadcn/ui` inputs without writing manual `fetch` or `axios` requests.

Here is the step-by-step guide to building the **Create Employee** page.

### Step 1: Initialize Form Components

Ensure you have the required `shadcn/ui` form elements installed in your project:

```bash
npx shadcn-ui@latest add label input select

```

### Step 2: Build the Create Page (`Create.tsx`)

Create a new file at `resources/js/Pages/HR/Employees/Create.tsx`.

Notice how `useForm` from Inertia handles the `data` state, the `setData` mutator, the `post` method, and catches all Laravel validation `errors` automatically.

```tsx
import React from 'react';
import { Head, Link, useForm } from '@inertiajs/react';
import { Button } from '@/Components/ui/button';
import { Input } from '@/Components/ui/input';
import { Label } from '@/Components/ui/label';

export default function EmployeeCreate() {
    // Inertia's useForm hook handles state and errors perfectly
    const { data, setData, post, processing, errors } = useForm({
        employee_code: '',
        first_name: '',
        last_name: '',
        email: '',
        phone: '',
        department: '',
        position: '',
        hire_date: '',
        status: 'active',
    });

    const submit = (e: React.FormEvent) => {
        e.preventDefault();
        // Posts exactly to the store method we made in the Controller
        post(route('hr.employees.store'));
    };

    return (
        <div className="p-6 max-w-2xl mx-auto">
            <Head title="Add Employee - HR" />
            
            <div className="flex justify-between items-center mb-6">
                <h1 className="text-2xl font-bold tracking-tight">Add New Employee</h1>
                <Link href={route('hr.employees.index')}>
                    <Button variant="outline">Back to Directory</Button>
                </Link>
            </div>

            <div className="border rounded-md p-6 bg-white shadow-sm">
                <form onSubmit={submit} className="space-y-6">
                    
                    {/* Employee Code & Status Row */}
                    <div className="grid grid-cols-2 gap-4">
                        <div className="space-y-2">
                            <Label htmlFor="employee_code">Employee Code</Label>
                            <Input 
                                id="employee_code" 
                                value={data.employee_code} 
                                onChange={e => setData('employee_code', e.target.value)} 
                                placeholder="EMP-001"
                            />
                            {errors.employee_code && <p className="text-sm text-red-500">{errors.employee_code}</p>}
                        </div>
                        <div className="space-y-2">
                            <Label htmlFor="status">Status</Label>
                            <select 
                                id="status"
                                className="flex h-10 w-full items-center justify-between rounded-md border border-input bg-background px-3 py-2 text-sm"
                                value={data.status}
                                onChange={e => setData('status', e.target.value)}
                            >
                                <option value="active">Active</option>
                                <option value="on_leave">On Leave</option>
                                <option value="terminated">Terminated</option>
                            </select>
                            {errors.status && <p className="text-sm text-red-500">{errors.status}</p>}
                        </div>
                    </div>

                    {/* Name Row */}
                    <div className="grid grid-cols-2 gap-4">
                        <div className="space-y-2">
                            <Label htmlFor="first_name">First Name</Label>
                            <Input 
                                id="first_name" 
                                value={data.first_name} 
                                onChange={e => setData('first_name', e.target.value)} 
                            />
                            {errors.first_name && <p className="text-sm text-red-500">{errors.first_name}</p>}
                        </div>
                        <div className="space-y-2">
                            <Label htmlFor="last_name">Last Name</Label>
                            <Input 
                                id="last_name" 
                                value={data.last_name} 
                                onChange={e => setData('last_name', e.target.value)} 
                            />
                            {errors.last_name && <p className="text-sm text-red-500">{errors.last_name}</p>}
                        </div>
                    </div>

                    {/* Contact Row */}
                    <div className="grid grid-cols-2 gap-4">
                        <div className="space-y-2">
                            <Label htmlFor="email">Email Address</Label>
                            <Input 
                                id="email" 
                                type="email"
                                value={data.email} 
                                onChange={e => setData('email', e.target.value)} 
                            />
                            {errors.email && <p className="text-sm text-red-500">{errors.email}</p>}
                        </div>
                        <div className="space-y-2">
                            <Label htmlFor="phone">Phone Number</Label>
                            <Input 
                                id="phone" 
                                value={data.phone} 
                                onChange={e => setData('phone', e.target.value)} 
                            />
                            {errors.phone && <p className="text-sm text-red-500">{errors.phone}</p>}
                        </div>
                    </div>

                    {/* Job Details Row */}
                    <div className="grid grid-cols-3 gap-4">
                        <div className="space-y-2">
                            <Label htmlFor="department">Department</Label>
                            <Input 
                                id="department" 
                                value={data.department} 
                                onChange={e => setData('department', e.target.value)} 
                            />
                            {errors.department && <p className="text-sm text-red-500">{errors.department}</p>}
                        </div>
                        <div className="space-y-2">
                            <Label htmlFor="position">Position</Label>
                            <Input 
                                id="position" 
                                value={data.position} 
                                onChange={e => setData('position', e.target.value)} 
                            />
                            {errors.position && <p className="text-sm text-red-500">{errors.position}</p>}
                        </div>
                        <div className="space-y-2">
                            <Label htmlFor="hire_date">Hire Date</Label>
                            <Input 
                                id="hire_date" 
                                type="date"
                                value={data.hire_date} 
                                onChange={e => setData('hire_date', e.target.value)} 
                            />
                            {errors.hire_date && <p className="text-sm text-red-500">{errors.hire_date}</p>}
                        </div>
                    </div>

                    <div className="flex justify-end pt-4 border-t">
                        <Button type="submit" disabled={processing}>
                            {processing ? 'Saving...' : 'Save Employee'}
                        </Button>
                    </div>
                </form>
            </div>
        </div>
    );
}

```

### Why this is Professional Grade:

1. **No Page Reloads:** When you click save, Inertia sends an XHR request. If Laravel's `StoreEmployeeRequest` validation fails (e.g., the email is already taken), it intercepts the response, injects the errors directly into the `errors` object, and updates the React UI instantly.
2. **Double Submission Prevention:** The `<Button>` is automatically disabled (`disabled={processing}`) while the request is in flight, preventing users from clicking "Save" twice and creating duplicate records.
3. **Clean Route Handling:** By using `route('hr.employees.store')`, we utilize Laravel's Ziggy package, ensuring our frontend URLs never hardcode paths like `/hr/employees/store`.

---

With the Database, Controller, and Frontend Form wired up, you now have a complete, fully functioning HR slice of the ERP.

**Shall we move to setting up `spatie/laravel-permission` next, so we can ensure only the "Super Admin" or "HR Manager" roles can actually access this URL and submit this form?**

---

Integrating **Role-Based Access Control (RBAC)** is what separates a simple web app from a true Enterprise system. In an ERP, a standard employee shouldn't even see the "Add Employee" button, an HR Manager can add/edit employees, and only a Super Admin can permanently delete records.

Here is the professional workflow for implementing `spatie/laravel-permission` across both your Laravel backend and React frontend.

---

### Step 1: Install and Configure the Package

First, pull the package into your project and set up the necessary database tables.

```bash
# 1. Install the package
composer require spatie/laravel-permission

# 2. Publish the migration and config files
php artisan vendor:publish --provider="Spatie\Permission\PermissionServiceProvider"

# 3. Clear your cache and run the migrations
php artisan optimize:clear
php artisan migrate

```

Next, open your `App\Models\User.php` file and add the `HasRoles` trait so your users can be assigned permissions.

```php
namespace App\Models;

use Illuminate\Foundation\Auth\User as Authenticatable;
use Illuminate\Notifications\Notifiable;
use Spatie\Permission\Traits\HasRoles; // <-- Import this

class User extends Authenticatable
{
    use Notifiable, HasRoles; // <-- Add it here

    // ... existing code
}

```

### Step 2: Seed the ERP Roles and Permissions

You don't want to manually create roles in the database. Create a Seeder to establish the foundational hierarchy of your ERP.

```bash
php artisan make:seeder RoleAndPermissionSeeder

```

Open `database/seeders/RoleAndPermissionSeeder.php`:

```php
namespace Database\Seeders;

use Illuminate\Database\Seeder;
use Spatie\Permission\Models\Role;
use Spatie\Permission\Models\Permission;
use App\Models\User;

class RoleAndPermissionSeeder extends Seeder
{
    public function run()
    {
        // Reset cached roles and permissions
        app()[\Spatie\Permission\PermissionRegistrar::class]->forgetCachedPermissions();

        // 1. Create Permissions
        Permission::create(['name' => 'view employees']);
        Permission::create(['name' => 'create employees']);
        Permission::create(['name' => 'edit employees']);
        Permission::create(['name' => 'delete employees']);

        // 2. Create Roles and assign created permissions
        $hrManagerRole = Role::create(['name' => 'HR Manager']);
        $hrManagerRole->givePermissionTo(['view employees', 'create employees', 'edit employees']);

        $staffRole = Role::create(['name' => 'Staff']);
        $staffRole->givePermissionTo(['view employees']);

        $superAdminRole = Role::create(['name' => 'Super Admin']);
        // (Super Admin gets all permissions via a Gate definition later)

        // 3. Create a test user and assign a role
        $user = User::factory()->create([
            'name' => 'PixelVite Admin',
            'email' => 'admin@pixelvite.com',
        ]);
        $user->assignRole($superAdminRole);
    }
}

```

Run the seeder: `php artisan db:seed --class=RoleAndPermissionSeeder`

### Step 3: Protect the Backend (Laravel Routes/Controllers)

We must ensure that even if someone guesses the URL to create an employee, the server will reject them if they lack permission.

In Laravel 11, you typically protect routes directly in your module's route file or via the controller's constructor. Let's protect the `EmployeeController` inside your HR module.

Open `Modules/HR/Http/Controllers/EmployeeController.php` and add middleware:

```php
namespace Modules\HR\Http\Controllers;

use App\Http\Controllers\Controller;
use Illuminate\Routing\Controllers\HasMiddleware;
use Illuminate\Routing\Controllers\Middleware;

class EmployeeController extends Controller implements HasMiddleware
{
    // Apply Spatie Middleware directly to controller methods
    public static function middleware(): array
    {
        return [
            new Middleware('permission:view employees', only: ['index', 'show']),
            new Middleware('permission:create employees', only: ['create', 'store']),
            new Middleware('permission:edit employees', only: ['edit', 'update']),
            new Middleware('permission:delete employees', only: ['destroy']),
        ];
    }
    
    // ... index, create, store methods remain the same
}

```

### Step 4: Share Permissions with React (Inertia Bridge)

This is a critical step for modern ERPs. Your React frontend needs to know what the current user is allowed to do so it can hide or show buttons accordingly.

Open `app/Http/Middleware/HandleInertiaRequests.php`. Update the `share` method to pass the user's roles and permissions down to the browser.

```php
public function share(Request $request): array
{
    return array_merge(parent::share($request), [
        'auth' => [
            'user' => $request->user(),
            // Expose roles and permissions to the frontend
            'roles' => $request->user() ? $request->user()->getRoleNames() : [],
            'permissions' => $request->user() ? $request->user()->getAllPermissions()->pluck('name') : [],
        ],
        'flash' => [
            'success' => fn () => $request->session()->get('success')
        ],
    ]);
}

```

### Step 5: Protect the Frontend (React UI)

Now, let's go back to your `EmployeeIndex.tsx` file and use this new data to conditionally render the "+ Add Employee" button. If the user is just a standard "Staff" member, the button won't even exist in the DOM.

Open `resources/js/Pages/HR/Employees/Index.tsx`:

```tsx
import React from 'react';
import { Head, Link, usePage } from '@inertiajs/react';
import { Button } from '@/Components/ui/button';
import { Table, TableBody, TableCell, TableHead, TableHeader, TableRow } from "@/Components/ui/table";

export default function EmployeeIndex({ employees }) {
    // 1. Grab the shared auth object from Inertia's usePage hook
    const { auth } = usePage().props as any;
    
    // 2. Create a simple helper function to check permissions
    const hasPermission = (permission: string) => {
        return auth.permissions.includes(permission);
    };

    return (
        <div className="p-6">
            <Head title="HR - Employees" />
            
            <div className="flex justify-between items-center mb-6">
                <h1 className="text-2xl font-bold tracking-tight">Employee Directory</h1>
                
                {/* 3. Conditionally render the button! */}
                {hasPermission('create employees') && (
                    <Link href={route('hr.employees.create')}>
                        <Button>+ Add Employee</Button>
                    </Link>
                )}
            </div>

            <div className="border rounded-md">
                <Table>
                    {/* ... Table Headers ... */}
                    <TableBody>
                        {employees.data.map((employee) => (
                            <TableRow key={employee.id}>
                                {/* ... Other Cells ... */}
                                <TableCell className="text-right">
                                    
                                    {/* Conditionally render the Edit link */}
                                    {hasPermission('edit employees') && (
                                        <Link href={route('hr.employees.edit', employee.id)} className="text-blue-600 hover:underline mr-4">
                                            Edit
                                        </Link>
                                    )}

                                </TableCell>
                            </TableRow>
                        ))}
                    </TableBody>
                </Table>
            </div>
        </div>
    );
}

```

### The Super Admin Override (Pro-Tip)

In an ERP, you don't want to manually assign every single permission to the "Super Admin". Laravel's `Gate` allows you to intercept all permission checks and instantly approve the Super Admin.

Add this to the `boot` method of your `App\Providers\AppServiceProvider.php`:

```php
use Illuminate\Support\Facades\Gate;

public function boot(): void
{
    // Implicitly grant "Super Admin" role all permissions
    Gate::before(function ($user, $ability) {
        return $user->hasRole('Super Admin') ? true : null;
    });
}

```

---

