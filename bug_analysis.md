# Bug Analysis: mirador-annotations

## Summary
I've identified several bugs in the mirador-annotations codebase, ranging from dependency issues to potential runtime errors in the JavaScript code.

## Major Bugs Found

### 1. **Critical: Dependency Version Conflict** 
**Location**: `package.json`  
**Severity**: High  
**Issue**: There is a peer dependency version conflict:
- `@psychobolt/react-paperjs@^1.0.0` is specified as a dependency
- `@psychobolt/react-paperjs-editor@^0.0.14` expects `@psychobolt/react-paperjs@^0.0.58` as peer dependency

**Impact**: This prevents successful installation with npm install, making the project unusable without `--legacy-peer-deps`.

**Fix**: Update the dependencies to compatible versions or use a specific version of `@psychobolt/react-paperjs` that satisfies both requirements.

### 2. **Critical: Potential Null Reference Error**
**Location**: `src/AnnotationCreation.js:53`  
**Severity**: High  
**Code**:
```javascript
if (props.annotation.target.selector) {
```

**Issue**: The code checks if `props.annotation.target.selector` exists, but it doesn't check if `props.annotation.target` exists first. If `props.annotation.target` is `null` or `undefined`, this will throw a runtime error.

**Impact**: This could cause the application to crash when trying to create annotations with malformed annotation objects.

**Fix**: Add a null check:
```javascript
if (props.annotation.target && props.annotation.target.selector) {
```

### 3. **Medium: Missing System Dependencies**
**Location**: npm install failure  
**Severity**: Medium  
**Issue**: The canvas package requires system dependencies like `pixman-1` that are missing:
```
Package pixman-1 was not found in the pkg-config search path
```

**Impact**: Prevents successful installation and building of the project.

**Fix**: Install required system dependencies:
```bash
sudo apt-get install libcairo2-dev libpango1.0-dev libjpeg-dev libgif-dev librsvg2-dev libpixman-1-dev
```

### 4. **Low: Outdated Dependencies**
**Location**: Multiple dependency warnings  
**Severity**: Low  
**Issue**: Multiple deprecated packages are being used:
- `babel-eslint@10.1.0` (deprecated, should use `@babel/eslint-parser`)
- `eslint@7.32.0` (no longer supported)
- `core-js@2.6.12` (outdated and not recommended)
- Multiple Material-UI v4 packages (deprecated)

**Impact**: Security vulnerabilities, performance issues, and compatibility problems with newer Node.js versions.

**Fix**: Update to newer versions of these packages.

## Additional Issues

### 5. **Code Quality: No Issues Found**
I searched for common JavaScript bugs but found the code is generally well-written:
- ✅ No undefined map operations
- ✅ No leftover console.log statements  
- ✅ No setState with undefined values
- ✅ Proper React key props in map operations
- ✅ Good error handling in most places

## Recommendations

1. **Immediate**: Fix the null reference bug in `AnnotationCreation.js`
2. **High Priority**: Resolve the dependency version conflicts
3. **Medium Priority**: Update system dependencies documentation
4. **Low Priority**: Migrate to newer versions of deprecated packages

The most critical issue is the null reference error which could cause runtime crashes in production.