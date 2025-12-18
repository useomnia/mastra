# @mastra/auth-auth0

## 1.0.0-beta.3

### Major Changes

- # Breaking Change: Auth0 Provider Security & Stability Improvements ([#10632](https://github.com/mastra-ai/mastra/pull/10632))

  This release introduces **three major breaking changes** to the Auth0 authentication provider. These updates make token verification safer, prevent server crashes, and ensure proper authorization checks.

  ***

  ## 🔥 1. Added Robust Error Handling in `authenticateToken()`

  **File:** `auth0/src/index.ts`

  ### Before

  ```ts
  async authenticateToken(token: string): Promise<Auth0User | null> {
    const JWKS = createRemoteJWKSet(new URL(`https://${this.domain}/.well-known/jwks.json`));

    const { payload } = await jwtVerify(token, JWKS, {
      issuer: `https://${this.domain}/`,
      audience: this.audience,
    });

    return payload;
  }
  ```

  ### After

  ```ts
  async authenticateToken(token: string): Promise<Auth0User | null> {
    try {
      const JWKS = createRemoteJWKSet(
        new URL(`https://${this.domain}/.well-known/jwks.json`)
      );

      const { payload } = await jwtVerify(token, JWKS, {
        issuer: `https://${this.domain}/`,
        audience: this.audience,
      });

      return payload;
    } catch (err) {
      return null;
    }
  }
  ```

  ### Why this matters
  - Prevents server crashes from unhandled JWT verification errors.
  - Ensures authentication failures fail safely instead of throwing.

  ***

  ## 🔥 2. Added Validation for Empty or Invalid Token Input

  **File:** `auth0/src/index.ts`

  ### Before

  ```ts
  async authenticateToken(token: string): Promise<Auth0User | null> {
    const JWKS = createRemoteJWKSet(new URL(`https://${this.domain}/.well-known/jwks.json`));

    const { payload } = await jwtVerify(token, JWKS, {
      issuer: `https://${this.domain}/`,
      audience: this.audience,
    });

    return payload;
  }
  ```

  ### After

  ```ts
  async authenticateToken(token: string): Promise<Auth0User | null> {
    if (!token || typeof token !== "string") {
      return null;
    }

    try {
      const JWKS = createRemoteJWKSet(
        new URL(`https://${this.domain}/.well-known/jwks.json`)
      );

      const { payload } = await jwtVerify(token, JWKS, {
        issuer: `https://${this.domain}/`,
        audience: this.audience,
      });

      return payload;
    } catch {
      return null;
    }
  }
  ```

  ### Why this matters
  - Prevents crashes when token is `null`, `undefined`, or empty.
  - Makes token verification predictable and safe.

  ***

  ## 🔥 3. Improved `authorizeUser()` With Real Security Checks

  **File:** `auth0/src/index.ts`

  ### Before

  ```ts
  async authorizeUser(user: Auth0User) {
    return !!user;
  }
  ```

  ### After

  ```ts
  async authorizeUser(user: Auth0User): Promise<boolean> {
    if (!user || !user.sub) return false;

    if (user.exp && user.exp * 1000 < Date.now()) {
      return false;
    }

    return true;
  }
  ```

  ### Why this matters
  - Prevents invalid payloads like `{}` or `{ exp: 0 }` from being accepted.
  - Enforces a baseline standard:
    - `sub` (user ID) must exist.
    - Token must not be expired.

  - Improves reliability and security across all Auth0-based flows.

  ***

  ## ✅ Summary of Breaking Changes
  - `authenticateToken()` now **fails safely** instead of throwing.
  - Empty or invalid tokens are now **rejected early**.
  - `authorizeUser()` now performs **meaningful security checks**.

  These changes improve stability, prevent runtime crashes, and enforce safer authentication & authorization behavior throughout the system.

  ```

  ```

## 1.0.0-beta.2

### Patch Changes

- Allow provider to pass through options to the auth config ([#10284](https://github.com/mastra-ai/mastra/pull/10284))

## 1.0.0-beta.1

### Patch Changes

- dependencies updates: ([#10132](https://github.com/mastra-ai/mastra/pull/10132))
  - Updated dependency [`jose@^6.1.1` ↗︎](https://www.npmjs.com/package/jose/v/6.1.1) (from `^6.0.12`, in `dependencies`)

## 1.0.0-beta.0

### Major Changes

- Bump minimum required Node.js version to 22.13.0 ([#9706](https://github.com/mastra-ai/mastra/pull/9706))

- Experimental auth -> auth ([#9660](https://github.com/mastra-ai/mastra/pull/9660))

- Mark as stable ([`83d5942`](https://github.com/mastra-ai/mastra/commit/83d5942669ce7bba4a6ca4fd4da697a10eb5ebdc))

## 0.10.5

### Patch Changes

- Update package.json and README ([#7886](https://github.com/mastra-ai/mastra/pull/7886))

## 0.10.5-alpha.0

### Patch Changes

- Update package.json and README ([#7886](https://github.com/mastra-ai/mastra/pull/7886))

## 0.10.4

### Patch Changes

- de3cbc6: Update the `package.json` file to include additional fields like `repository`, `homepage` or `files`.

## 0.10.4-alpha.0

### Patch Changes

- [#7343](https://github.com/mastra-ai/mastra/pull/7343) [`de3cbc6`](https://github.com/mastra-ai/mastra/commit/de3cbc61079211431bd30487982ea3653517278e) Thanks [@LekoArts](https://github.com/LekoArts)! - Update the `package.json` file to include additional fields like `repository`, `homepage` or `files`.

## 0.10.3

### Patch Changes

- [`c6113ed`](https://github.com/mastra-ai/mastra/commit/c6113ed7f9df297e130d94436ceee310273d6430) Thanks [@wardpeet](https://github.com/wardpeet)! - Fix peerdpes for @mastra/core

## 0.10.2

### Patch Changes

- 4a406ec: fixes TypeScript declaration file imports to ensure proper ESM compatibility

## 0.10.2-alpha.0

### Patch Changes

- 4a406ec: fixes TypeScript declaration file imports to ensure proper ESM compatibility

## 0.10.1

### Patch Changes

- ee857ae: dependencies updates:
  - Updated dependency [`jose@^6.0.12` ↗︎](https://www.npmjs.com/package/jose/v/6.0.12) (from `^6.0.11`, in `dependencies`)

## 0.10.1-alpha.0

### Patch Changes

- ee857ae: dependencies updates:
  - Updated dependency [`jose@^6.0.12` ↗︎](https://www.npmjs.com/package/jose/v/6.0.12) (from `^6.0.11`, in `dependencies`)
