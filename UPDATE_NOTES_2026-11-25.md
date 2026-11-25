# BSP-SMS update notes — 25 Nov 2026

## Result Ready (Tool 7)
- Prevented repeated/canonical subject rows from creating duplicate subject columns in the exported workbook. Continued rows now fill only missing marks in the existing subject record.
- Corrected the fallback full-mark schema to match the supplied Result Ready screenshot: Bengali, English, Mathematics and Our Environment = 20/30/50 (100); Art & Work Education and Health & Physical Education = 10/15/25 (50).
- Corrected the fallback subject order to match the portal result table.

## Existing app structure preserved
- Existing tool entry points, authentication/backend files, Google Apps Script backend, and GitHub Actions workflow are retained.
- The current hub already uses individual tool entry paths and opens each tool in its own tool screen/frame; native Result Ready, Bulk Marksheet and HPRC Scan screens continue to use their existing native routes.

## Validation note
- ZIP structure and edited source files were checked. An Android APK build could not be run in this environment because the project has no `gradlew` script / wrapper JAR and no system Gradle executable. Please run the included GitHub Actions workflow to compile the APK before deployment.
