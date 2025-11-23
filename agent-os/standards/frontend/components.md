## UI component best practices

### General Principles
- **Single Responsibility**: Each component should have one clear purpose and do it well
- **Reusability**: Design components to be reused across different contexts with configurable props
- **Composability**: Build complex UIs by combining smaller, simpler components
- **Clear Interface**: Define explicit, well-typed props with sensible defaults
- **Encapsulation**: Keep internal implementation details private; expose only necessary APIs
- **Consistent Naming**: Use PascalCase for components, camelCase for props
- **State Management**: Keep state as local as possible; use React Context or Zustand for shared state
- **Minimal Props**: Keep props manageable; prefer composition over prop drilling
- **Documentation**: Use TypeScript types for self-documenting components

### AugentisLabs Next.js 14 Patterns

#### App Router Structure
Use Next.js 14 App Router with server/client component separation:

```
app/
├── layout.tsx                   # Root layout with providers
├── page.tsx                     # Landing page (server component)
├── dashboard/
│   ├── layout.tsx               # Dashboard layout with sidebar (server component)
│   ├── page.tsx                 # Dashboard home
│   ├── ventures/
│   │   ├── [id]/
│   │   │   ├── page.tsx         # Venture detail (server component)
│   │   │   ├── discover/        # Discover phase UI
│   │   │   ├── define/          # Define phase UI
│   │   │   └── ...
│   │   └── create/
│   │       └── page.tsx         # Create venture (client component with forms)
│   ├── artifacts/
│   ├── gates/
│   └── telemetry/
```

#### Server Components (Default)
Use server components by default for:
- Static content rendering
- Data fetching from API
- SEO-critical pages
- Layout components

```typescript
// app/dashboard/ventures/[id]/page.tsx (Server Component)
import { VentureDetails } from '@/components/ventures/VentureDetails';

interface PageProps {
  params: { id: string };
}

export default async function VenturePage({ params }: PageProps) {
  // Fetch data directly in server component
  const venture = await fetch(`/api/v1/ventures/${params.id}`).then(res => res.json());

  return <VentureDetails venture={venture} />;
}
```

#### Client Components (Explicit 'use client')
Mark components with `'use client'` directive when using:
- React hooks (useState, useEffect, etc.)
- Browser APIs (localStorage, window, etc.)
- Event handlers (onClick, onChange, etc.)
- Third-party libraries that use hooks

```typescript
'use client';

import { useState } from 'react';
import { Button } from '@/components/ui/Button';

export function CreateVentureForm() {
  const [name, setName] = useState('');

  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault();
    // Submit logic
  };

  return (
    <form onSubmit={handleSubmit}>
      <input value={name} onChange={(e) => setName(e.target.value)} />
      <Button type="submit">Create Venture</Button>
    </form>
  );
}
```

#### Component Organization
```
components/
├── ui/                          # Reusable UI primitives
│   ├── Button.tsx
│   ├── Input.tsx
│   ├── Card.tsx
│   ├── Modal.tsx
│   └── ...
├── ventures/                    # Venture-specific components
│   ├── VentureCard.tsx
│   ├── VentureList.tsx
│   ├── VenturePhaseIndicator.tsx
│   └── CreateVentureForm.tsx
├── gates/                       # Quality gate components
│   ├── GateScoreCard.tsx
│   ├── GateCriteriaList.tsx
│   └── GateOverrideModal.tsx
├── agents/                      # Agent-related components
│   ├── AgentCard.tsx
│   ├── AgentExecutionStatus.tsx
│   └── AgentOutputViewer.tsx
├── artifacts/                   # Artifact viewer components
│   ├── ArtifactViewer.tsx
│   ├── ArtifactVersionHistory.tsx
│   └── ArtifactExport.tsx
├── telemetry/                   # Telemetry visualization
│   ├── DivergenceAlertCard.tsx
│   ├── MetricsChart.tsx
│   └── TelemetryDashboard.tsx
└── layout/                      # Layout components
    ├── Sidebar.tsx
    ├── Header.tsx
    └── Footer.tsx
```

#### TypeScript Component Props
Always use TypeScript interfaces for props:

```typescript
interface VentureCardProps {
  venture: {
    id: string;
    name: string;
    description: string;
    phase: Phase;
    createdAt: string;
  };
  onEdit?: (id: string) => void;
  onDelete?: (id: string) => void;
  className?: string;
}

export function VentureCard({ venture, onEdit, onDelete, className }: VentureCardProps) {
  return (
    <div className={cn('rounded-lg border p-4', className)}>
      <h3 className="font-semibold">{venture.name}</h3>
      <p className="text-sm text-muted-foreground">{venture.description}</p>
      <VenturePhaseIndicator phase={venture.phase} />
    </div>
  );
}
```

#### Tailwind CSS Styling
Use Tailwind CSS utility classes for styling:

```typescript
export function Button({ variant = 'primary', size = 'md', children, ...props }: ButtonProps) {
  return (
    <button
      className={cn(
        'rounded-md font-medium transition-colors',
        variant === 'primary' && 'bg-blue-600 text-white hover:bg-blue-700',
        variant === 'secondary' && 'bg-gray-200 text-gray-900 hover:bg-gray-300',
        size === 'sm' && 'px-3 py-1.5 text-sm',
        size === 'md' && 'px-4 py-2 text-base',
        size === 'lg' && 'px-6 py-3 text-lg',
        props.className
      )}
      {...props}
    >
      {children}
    </button>
  );
}
```

**Styling Guidelines**:
- Use `cn()` utility (from `clsx` + `tailwind-merge`) to merge class names
- Avoid inline styles; use Tailwind utilities instead
- Create reusable variants with `cva()` (class-variance-authority) for complex components
- Use Tailwind's responsive prefixes: `sm:`, `md:`, `lg:`, `xl:`, `2xl:`
- Follow Tailwind's design system: spacing (4px increments), colors, typography

#### State Management Patterns

**Local State (useState)**:
```typescript
'use client';

export function VentureFilter() {
  const [phase, setPhase] = useState<Phase | 'all'>('all');
  const [search, setSearch] = useState('');

  return (
    <div>
      <Input value={search} onChange={(e) => setSearch(e.target.value)} placeholder="Search..." />
      <Select value={phase} onChange={setPhase}>
        <option value="all">All Phases</option>
        <option value="DISCOVER">Discover</option>
        {/* ... */}
      </Select>
    </div>
  );
}
```

**Shared State (React Context)**:
```typescript
'use client';

import { createContext, useContext, useState } from 'react';

interface VentureContextType {
  currentVenture: Venture | null;
  setCurrentVenture: (venture: Venture | null) => void;
}

const VentureContext = createContext<VentureContextType | undefined>(undefined);

export function VentureProvider({ children }: { children: React.ReactNode }) {
  const [currentVenture, setCurrentVenture] = useState<Venture | null>(null);

  return (
    <VentureContext.Provider value={{ currentVenture, setCurrentVenture }}>
      {children}
    </VentureContext.Provider>
  );
}

export function useVenture() {
  const context = useContext(VentureContext);
  if (!context) throw new Error('useVenture must be used within VentureProvider');
  return context;
}
```

**Form State (React Hook Form + Zod)**:
```typescript
'use client';

import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { z } from 'zod';

const ventureSchema = z.object({
  name: z.string().min(3).max(100),
  description: z.string().min(10).max(500),
  phase: z.enum(['DISCOVER', 'DEFINE', 'DESIGN', 'DEVELOP', 'DEPLOY']),
});

type VentureFormData = z.infer<typeof ventureSchema>;

export function CreateVentureForm() {
  const { register, handleSubmit, formState: { errors } } = useForm<VentureFormData>({
    resolver: zodResolver(ventureSchema)
  });

  const onSubmit = async (data: VentureFormData) => {
    // Submit to API
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <Input {...register('name')} error={errors.name?.message} />
      <Input {...register('description')} error={errors.description?.message} />
      <Button type="submit">Create</Button>
    </form>
  );
}
```

#### Data Fetching Patterns

**Server-Side Fetching (Server Components)**:
```typescript
// app/dashboard/ventures/page.tsx
export default async function VenturesPage() {
  const ventures = await fetch('/api/v1/ventures', {
    headers: { Authorization: `Bearer ${getServerToken()}` }
  }).then(res => res.json());

  return <VentureList ventures={ventures} />;
}
```

**Client-Side Fetching (Custom Hook)**:
```typescript
'use client';

import useSWR from 'swr';

export function useVenture(id: string) {
  const { data, error, isLoading } = useSWR(`/api/v1/ventures/${id}`, fetcher);

  return {
    venture: data,
    isLoading,
    isError: error
  };
}

// Usage
export function VentureDetails({ id }: { id: string }) {
  const { venture, isLoading, isError } = useVenture(id);

  if (isLoading) return <Skeleton />;
  if (isError) return <ErrorMessage />;

  return <div>{venture.name}</div>;
}
```

#### Loading States
Use Next.js loading.tsx for route-level loading states:

```typescript
// app/dashboard/ventures/[id]/loading.tsx
export default function Loading() {
  return <VentureSkeleton />;
}

// components/ventures/VentureSkeleton.tsx
export function VentureSkeleton() {
  return (
    <div className="space-y-4">
      <div className="h-8 w-64 bg-gray-200 animate-pulse rounded" />
      <div className="h-4 w-full bg-gray-200 animate-pulse rounded" />
      <div className="h-4 w-3/4 bg-gray-200 animate-pulse rounded" />
    </div>
  );
}
```

#### Error Handling
Use Next.js error.tsx for route-level error boundaries:

```typescript
'use client';

// app/dashboard/ventures/[id]/error.tsx
export default function Error({ error, reset }: { error: Error; reset: () => void }) {
  return (
    <div className="flex flex-col items-center justify-center p-8">
      <h2 className="text-2xl font-bold text-red-600">Something went wrong!</h2>
      <p className="mt-2 text-gray-600">{error.message}</p>
      <Button onClick={reset} className="mt-4">Try again</Button>
    </div>
  );
}
```

#### Performance Optimization

**React.memo for Expensive Components**:
```typescript
export const VentureCard = React.memo(({ venture }: VentureCardProps) => {
  return <div>{venture.name}</div>;
});
```

**Lazy Loading**:
```typescript
'use client';

import dynamic from 'next/dynamic';

const ArtifactViewer = dynamic(() => import('@/components/artifacts/ArtifactViewer'), {
  loading: () => <Skeleton />,
  ssr: false // Disable SSR for client-only components
});
```

**Image Optimization**:
```typescript
import Image from 'next/image';

export function VentureLogo({ src, alt }: { src: string; alt: string }) {
  return (
    <Image
      src={src}
      alt={alt}
      width={64}
      height={64}
      className="rounded-full"
      priority={false}
    />
  );
}
```

#### Accessibility (a11y)
- **Semantic HTML**: Use proper HTML5 elements (`<button>`, `<nav>`, `<main>`, etc.)
- **ARIA Labels**: Add `aria-label` for icon-only buttons
- **Keyboard Navigation**: Ensure all interactive elements are keyboard accessible
- **Focus Indicators**: Use `focus-visible:ring-2` for visible focus states
- **Alt Text**: Always provide alt text for images
- **Color Contrast**: Ensure WCAG AA compliance (4.5:1 contrast ratio)

```typescript
export function IconButton({ icon: Icon, label, onClick }: IconButtonProps) {
  return (
    <button
      onClick={onClick}
      aria-label={label}
      className="p-2 rounded hover:bg-gray-100 focus-visible:ring-2 focus-visible:ring-blue-500"
    >
      <Icon className="h-5 w-5" />
    </button>
  );
}
```

#### 5D Phase-Specific Components

**Phase Indicator**:
```typescript
const phaseConfig = {
  DISCOVER: { color: 'blue', label: 'Discover', icon: SearchIcon },
  DEFINE: { color: 'green', label: 'Define', icon: FileTextIcon },
  DESIGN: { color: 'purple', label: 'Design', icon: PenToolIcon },
  DEVELOP: { color: 'orange', label: 'Develop', icon: CodeIcon },
  DEPLOY: { color: 'red', label: 'Deploy', icon: RocketIcon },
};

export function VenturePhaseIndicator({ phase }: { phase: Phase }) {
  const config = phaseConfig[phase];
  const Icon = config.icon;

  return (
    <div className={`flex items-center gap-2 text-${config.color}-600`}>
      <Icon className="h-4 w-4" />
      <span className="text-sm font-medium">{config.label}</span>
    </div>
  );
}
```

**Quality Gate Score Card**:
```typescript
export function GateScoreCard({ gate }: { gate: QualityGate }) {
  const percentage = Math.round(gate.score * 100);
  const passed = gate.passed;

  return (
    <Card>
      <CardHeader>
        <CardTitle>{gate.gateType} Gate</CardTitle>
      </CardHeader>
      <CardContent>
        <div className="flex items-center justify-between">
          <div className="text-3xl font-bold">{percentage}%</div>
          <Badge variant={passed ? 'success' : 'destructive'}>
            {passed ? 'Passed' : 'Failed'}
          </Badge>
        </div>
        <Progress value={percentage} className="mt-4" />
        <p className="mt-2 text-sm text-muted-foreground">
          Threshold: {Math.round(gate.threshold * 100)}%
        </p>
      </CardContent>
    </Card>
  );
}
```

**Agent Execution Status**:
```typescript
export function AgentExecutionStatus({ execution }: { execution: AgentExecution }) {
  const statusConfig = {
    PENDING: { color: 'gray', icon: ClockIcon },
    RUNNING: { color: 'blue', icon: LoaderIcon, animate: true },
    COMPLETED: { color: 'green', icon: CheckCircleIcon },
    FAILED: { color: 'red', icon: XCircleIcon },
    CANCELLED: { color: 'gray', icon: XIcon },
  };

  const config = statusConfig[execution.status];
  const Icon = config.icon;

  return (
    <div className={`flex items-center gap-2 text-${config.color}-600`}>
      <Icon className={`h-4 w-4 ${config.animate ? 'animate-spin' : ''}`} />
      <span className="text-sm">{execution.status}</span>
    </div>
  );
}
```

#### Performance Targets
- **Largest Contentful Paint (LCP)**: <2 seconds
- **First Input Delay (FID)**: <100ms
- **Cumulative Layout Shift (CLS)**: <0.1
- **Component Render Time**: <50ms for non-data-fetching components
- **Bundle Size**: Keep page bundles <200KB (gzipped)

#### Testing Components
Use React Testing Library for component tests:

```typescript
import { render, screen, fireEvent } from '@testing-library/react';
import { CreateVentureForm } from './CreateVentureForm';

describe('CreateVentureForm', () => {
  it('submits form with valid data', async () => {
    const onSubmit = jest.fn();
    render(<CreateVentureForm onSubmit={onSubmit} />);

    fireEvent.change(screen.getByLabelText('Name'), { target: { value: 'My Venture' } });
    fireEvent.change(screen.getByLabelText('Description'), { target: { value: 'A great idea' } });
    fireEvent.click(screen.getByText('Create'));

    await waitFor(() => {
      expect(onSubmit).toHaveBeenCalledWith({ name: 'My Venture', description: 'A great idea' });
    });
  });
});
```
