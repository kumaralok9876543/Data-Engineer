# Build Error Analysis & Fix

## Problem
The build fails with TanStack Start import protection error:
- Cannot import from `src/server/jobs.functions.ts` in client code
- Pattern blocked: `**/server/**`  
- File: `src/routes/dashboard.tsx` imports from `src/server/jobs.functions`

## Root Cause
TanStack Start's import protection doesn't allow imports from `/server/` directories in client components, even if the functions are wrapped with `createServerFn()`.

## Solution
Rename `src/server/jobs.functions.ts` to `src/jobs.server.ts` to follow TanStack Start conventions for server functions.

## Steps to Fix

1. Rename the file: Move `src/server/jobs.functions.ts` → `src/jobs.server.ts`
2. Update import in `src/routes/dashboard.tsx`: 
   - FROM: `import { fetchJobsFromLinkedIn, repairExistingJobs } from "@/server/jobs.functions";`
   - TO: `import { fetchJobsFromLinkedIn, repairExistingJobs } from "@/jobs.server";`

## Next Steps After Fix
Run build again: `npm run build`
