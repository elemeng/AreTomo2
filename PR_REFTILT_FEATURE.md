## Summary

Added a new CLI option `-RefTilt` that allows users to specify which tilt angle's image should be used as the reference for alignment operations, instead of always using the zero-tilt image. This feature is particularly useful for FIB lamella datasets and other non-standard tilt series acquisitions.

## Type of Change
- [x] New feature
- [ ] Bug fix
- [ ] Breaking change
- [ ] Documentation update
- [ ] Performance improvement
- [ ] Code refactoring
- [ ] Tests
- [ ] CI/CD

## Changes Made
- **CInput.h/cpp**: Added `-RefTilt` CLI parameter with default value of 0.0 degrees
- **MrcUtil/CRemoveDarkFrames.cpp**: Modified `sGetZeroTiltIdx` to accept target tilt parameter
- **PatchAlign/CLocalAlign.cpp**: Updated to use user-specified reference tilt
- **ProjAlign/CProjAlignMain.cpp**: Updated main projection alignment
- **Massnorm/CLinearNorm.cpp**: Updated mass normalization reference
- **PatchAlign/CFitPatchShifts.cpp**: Updated patch shift fitting
- **TiltOffset/CTiltAxisMain.cpp & CTiltOffsetMain.cpp**: Updated tilt calculations

## Testing
- [x] Local testing completed
- [ ] Unit tests added/updated
- [ ] Integration tests pass
- [x] Manual testing performed

## Test Plan
1. Test with standard tilt series (0° center) - should behave identically to before
2. Test with FIB lamella data (-12° or +12°) using `-RefTilt Val`
3. Test with offset tilt series using other `-RefTilt` value
4. Verify help text displays correctly with `AreTomo2 --help`

## Backward Compatibility
- [x] Changes are backward compatible
- [ ] Breaking changes documented
- [ ] Migration guide provided

**Backward Compatible**: Default value is 0.0 degrees, maintaining existing behavior for all current users.

## Performance Impact
- [x] No performance impact
- [ ] Performance improved
- [ ] Performance impact documented below

## Documentation
- [x] Code is self-documenting
- [ ] API documentation updated
- [ ] User guide updated

## Key Benefits
1. **FIB Lamella Support**: Properly handles tilt series that don't start from 0°
2. **Flexibility**: Users can choose optimal reference for their specific samples
3. **Backward Compatible**: Default 0.0° maintains existing behavior
4. **Comprehensive**: Updates all alignment components consistently

## Checklist
- [x] Code follows project style guidelines
- [x] Self-review completed
- [x] Comments added for complex logic
- [x] No debugging code/comments left
- [x] Error handling implemented
- [x] Edge cases considered

## Additional Notes
The `-RefTilt` parameter now propagates through the entire alignment pipeline:
- Dark frame detection uses it as reference for threshold calculation
- Global alignment uses it as reference frame for correlation  
- Local patch alignment uses it for patch-based motion correction
- Mass normalization uses it as intensity reference
- Tilt axis/offset calculations use it as geometric reference

This provides complete control over the reference tilt image throughout processing, making AreTomo2 much more flexible for non-standard acquisitions.