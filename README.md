# Task Manager – Penetration Test Report

**Date:** 30 October 2026  
**Tester:** Harrison Daniel  
**Application:** Vulnerable Task Manager (Next.js)  
**Scope:** `http://localhost:3000`  
**Overall Risk:** High / Critical

---

## Executive Summary

The application contains multiple high and critical vulnerabilities that allow a low-privileged user to:

- View, edit, and delete other users’ tasks (IDOR)
- Access administrative endpoints
- Forge JWT tokens and escalate to admin privileges

These issues enable full compromise of user data and privilege escalation.

---

## Findings

### 1. Insecure Direct Object Reference (IDOR) – Tasks
**Severity:** High  
**Endpoint:** `GET/PUT/DELETE /api/tasks/{id}`

Any authenticated user can read, modify, or delete tasks belonging to other users by changing the task ID. No ownership validation is performed.

**Impact:** Unauthorized data access and modification.

**Remediation:** Check that `task.ownerId === currentUser.id` before allowing access.

---

### 2. Broken Access Control – Admin Endpoint
**Severity:** High  
**Endpoint:** `GET /api/admin/users`

Normal users can access the admin users list. No role verification is enforced.

**Impact:** Information disclosure and vertical privilege escalation.

**Remediation:** Restrict the endpoint to users with `role === "admin"`.

---

### 3. Weak JWT Signing Secret
**Severity:** Critical  
**Location:** `src/lib/auth.ts`

JWT tokens are signed with the weak secret `"secret"`. This can be easily cracked, allowing attackers to forge tokens with any role (including admin).

**Impact:** Complete authentication bypass and privilege escalation.

**Remediation:**  
- Use a strong, randomly generated secret  
- Store it in environment variables  
- Prefer RS256 where possible  
- Implement short expiry + token rotation

---

## Summary of Recommendations

1. Replace the weak JWT secret immediately  
2. Add ownership checks on all task/order endpoints  
3. Enforce role-based access control on admin routes  
4. Filter list endpoints so users only see their own data  

---

## Conclusion

The application is vulnerable to critical Broken Access Control and Cryptographic failures. All findings are easily exploitable and should be fixed before any real-world deployment.

**Report by:** Harrison Daniel  
**Date:** 30 October 2026"# vuln-task-manager" 
