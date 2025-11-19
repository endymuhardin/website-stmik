# Shared Code TODO - TypeScript Types & Constants

## Status: NOT STARTED (Deferred to Phase 5)

This directory will contain TypeScript types, constants, and validators shared between frontend and backend.

**Purpose:** Single source of truth for data structures used across the monorepo

---

## Phase 5: Shared Code Implementation

### TypeScript Types (`types/`)

#### User Types (`types/User.ts`)
```typescript
export interface User {
  id: number;
  email: string;
  name: string;
  role: 'registrant' | 'staff';
  provider: 'google' | 'local';
  providerId?: string;
  emailVerified: boolean;
  createdAt: Date;
  updatedAt: Date;
}

export interface UserRegistration {
  email: string;
  password: string;
  name: string;
}

export interface UserLogin {
  email: string;
  password: string;
}

export interface GoogleAuthData {
  email: string;
  name: string;
  providerId: string;
}
```

#### Application Types (`types/Application.ts`)
```typescript
export type ApplicationStatus = 'draft' | 'pending' | 'approved' | 'rejected';

export interface Application {
  id: number;
  userId: number;
  program: string;
  status: ApplicationStatus;
  personalInfo: {
    fullName: string;
    dateOfBirth: string;
    phone: string;
    address: string;
  };
  education: {
    lastSchool: string;
    graduationYear: number;
    gpa?: number;
  };
  documents: {
    transcript?: string;  // File URL
    certificate?: string; // File URL
    photo?: string;       // File URL
  };
  submittedAt?: Date;
  reviewedAt?: Date;
  reviewedBy?: number;
  reviewNotes?: string;
  createdAt: Date;
  updatedAt: Date;
}

export interface ApplicationSubmission {
  program: string;
  personalInfo: Application['personalInfo'];
  education: Application['education'];
  documents: Application['documents'];
}
```

#### Auth Types (`types/Auth.ts`)
```typescript
export interface JWTPayload {
  userId: number;
  email: string;
  role: 'registrant' | 'staff';
  provider: 'google' | 'local';
}

export interface AuthResponse {
  token: string;
  user: Omit<User, 'passwordHash'>;
}
```

#### File Types (`types/File.ts`)
```typescript
export interface FileUpload {
  id: string;
  userId: number;
  filename: string;
  originalName: string;
  mimeType: string;
  size: number;
  url: string;
  createdAt: Date;
}

export type AllowedFileType = 'application/pdf' | 'image/jpeg' | 'image/png';
```

### Constants (`constants/`)

#### Application Constants (`constants/applicationStatus.ts`)
```typescript
export const APPLICATION_STATUS = {
  DRAFT: 'draft',
  PENDING: 'pending',
  APPROVED: 'approved',
  REJECTED: 'rejected',
} as const;

export const APPLICATION_STATUS_LABELS = {
  [APPLICATION_STATUS.DRAFT]: 'Draft',
  [APPLICATION_STATUS.PENDING]: 'Under Review',
  [APPLICATION_STATUS.APPROVED]: 'Approved',
  [APPLICATION_STATUS.REJECTED]: 'Rejected',
};
```

#### User Roles (`constants/userRoles.ts`)
```typescript
export const USER_ROLES = {
  REGISTRANT: 'registrant',
  STAFF: 'staff',
} as const;

export const USER_ROLE_PERMISSIONS = {
  [USER_ROLES.REGISTRANT]: ['view:own-applications', 'create:application', 'update:own-draft'],
  [USER_ROLES.STAFF]: ['view:all-applications', 'update:application-status', 'view:users'],
};
```

#### Programs (`constants/programs.ts`)
```typescript
export const PROGRAMS = {
  COMPUTER_SCIENCE: 'computer-science',
  INFORMATION_SYSTEMS: 'information-systems',
  SOFTWARE_ENGINEERING: 'software-engineering',
} as const;

export const PROGRAM_LABELS = {
  [PROGRAMS.COMPUTER_SCIENCE]: 'Computer Science',
  [PROGRAMS.INFORMATION_SYSTEMS]: 'Information Systems',
  [PROGRAMS.SOFTWARE_ENGINEERING]: 'Software Engineering',
};
```

#### File Upload Limits (`constants/fileUpload.ts`)
```typescript
export const FILE_UPLOAD_LIMITS = {
  MAX_FILE_SIZE: 5 * 1024 * 1024, // 5MB
  ALLOWED_TYPES: ['application/pdf', 'image/jpeg', 'image/png'],
  MAX_FILES_PER_APPLICATION: 5,
};

export const FILE_TYPES = {
  PDF: 'application/pdf',
  JPEG: 'image/jpeg',
  PNG: 'image/png',
} as const;
```

### Validators (`validators/`)

**Note:** Use Zod for runtime validation

#### Application Validator (`validators/applicationSchema.ts`)
```typescript
import { z } from 'zod';

export const personalInfoSchema = z.object({
  fullName: z.string().min(3).max(100),
  dateOfBirth: z.string().regex(/^\d{4}-\d{2}-\d{2}$/),
  phone: z.string().regex(/^[0-9]{10,15}$/),
  address: z.string().min(10).max(500),
});

export const educationSchema = z.object({
  lastSchool: z.string().min(3).max(200),
  graduationYear: z.number().int().min(1990).max(new Date().getFullYear()),
  gpa: z.number().min(0).max(4).optional(),
});

export const applicationSubmissionSchema = z.object({
  program: z.enum(['computer-science', 'information-systems', 'software-engineering']),
  personalInfo: personalInfoSchema,
  education: educationSchema,
  documents: z.object({
    transcript: z.string().url().optional(),
    certificate: z.string().url().optional(),
    photo: z.string().url().optional(),
  }),
});
```

#### User Validator (`validators/userSchema.ts`)
```typescript
import { z } from 'zod';

export const userRegistrationSchema = z.object({
  email: z.string().email(),
  password: z.string().min(8).max(100),
  name: z.string().min(3).max(100),
});

export const userLoginSchema = z.object({
  email: z.string().email(),
  password: z.string().min(1),
});
```

---

## Implementation Checklist

### Types
- [ ] Create `types/User.ts`
- [ ] Create `types/Application.ts`
- [ ] Create `types/Auth.ts`
- [ ] Create `types/File.ts`
- [ ] Create `types/index.ts` (barrel export)

### Constants
- [ ] Create `constants/applicationStatus.ts`
- [ ] Create `constants/userRoles.ts`
- [ ] Create `constants/programs.ts`
- [ ] Create `constants/fileUpload.ts`
- [ ] Create `constants/index.ts` (barrel export)

### Validators
- [ ] Install Zod: `npm install zod`
- [ ] Create `validators/applicationSchema.ts`
- [ ] Create `validators/userSchema.ts`
- [ ] Create `validators/index.ts` (barrel export)

### Configuration
- [ ] Create `package.json` for shared package
- [ ] Configure TypeScript (`tsconfig.json`)
- [ ] Set up build script (compile to `dist/`)
- [ ] Configure as npm workspace

### Usage Setup
- [ ] Import in frontend: `import { User } from '@shared/types'`
- [ ] Import in backend: `import { User } from '@shared/types'`
- [ ] Configure path aliases in tsconfig files

---

## Package.json (Example)

```json
{
  "name": "@campus/shared",
  "version": "1.0.0",
  "main": "dist/index.js",
  "types": "dist/index.d.ts",
  "scripts": {
    "build": "tsc",
    "watch": "tsc --watch"
  },
  "devDependencies": {
    "typescript": "^5.0.0"
  },
  "dependencies": {
    "zod": "^3.22.0"
  }
}
```

---

## Benefits

✅ **Single Source of Truth:** Types defined once, used everywhere
✅ **Type Safety:** Compile-time checks prevent bugs
✅ **Consistency:** Same validation logic across frontend/backend
✅ **Maintainability:** Update types in one place
✅ **Auto-complete:** Better developer experience with IDE support

---

## Timeline Estimate

- **Types:** 1 day
- **Constants:** 0.5 day
- **Validators:** 1 day
- **Setup & Configuration:** 0.5 day

**Total: 3 days**

---

## Success Criteria

- [ ] All types exported from `types/index.ts`
- [ ] All constants exported from `constants/index.ts`
- [ ] All validators exported from `validators/index.ts`
- [ ] Frontend can import and use shared types
- [ ] Backend can import and use shared types
- [ ] TypeScript compilation successful
- [ ] No type errors in frontend or backend when using shared types
