# Header, scope, and conventions

Web app, Disease Detection, Pest Detection

# Data model, the D1 diagram

# Core algorithms

## 1. Single-photo disease classification

**Source:** `docker_app/backend/main.py`, `leaf_preprocess.py`.

```text
Decode the uploaded image.
Build leaf, lesion, dark-mark, and edge masks using OpenCV.
Stack grayscale, combined lesion/dark-mark mask, and edges; resize to 224x224.
Apply the saved image processor and run the ViT model in evaluation mode.
Apply softmax; return the highest-scoring label and up to three ranked classes.
Round confidence values to four decimal places.
```

Leaf preprocessing is enabled by default and can be disabled through `DISABLE_LEAF_PREPROCESS`. Flutter labels confidence as high above 0.85, moderate above 0.6, and low otherwise. These labels describe confidence, not disease severity. The backend always returns its top class.

## 2. Multispectral disease analysis

**Source:** `application/Docker/Multispectral_Container/`.

```text
Save at least five TIFF uploads using a supplied or generated capture ID.
Sort bands and align them to the third image using ORB matches and a homography.
Generate the band collage, spectral-index previews, and statistics.
Read the five aligned bands as blue, green, red, NIR, and red-edge.
Compute NDVI = (NIR-red)/(NIR+red+1e-6).
Compute NDRE = (NIR-redEdge)/(NIR+redEdge+1e-6).
Normalize the resulting seven channels using saved means and standard deviations.
For each complete 64x64 tile, with stride 32:
    Skip tiles with vine ResNet-18 probability below 0.5.
    Run the distilled disease ResNet-18 and accumulate its probability heatmap.
    For disease probability >= 0.9, count a positive tile and generate CAM boxes.
Set disease_detected to whether any disease-positive tile exists.
Save visualization PNGs and a JSON summary.
Upload generated JPG/PNG/JSON outputs, make them public, and write Firestore metadata.
Optionally clean local files, then return the completed result.
```

Missing reference-image features stop alignment; failed alignment of another band retains that band's original image. Band order comes from filenames. No qualifying vine tiles produces `disease_detected: false`. CAM boxes are drawn into images rather than returned as structured detections. Processing completes within the HTTP request; uploads and the Firestore write are separate operations.

## 3. Pest classification

**Source:** `application/pest_detection_backend/main.py`.

```text
Decode the image to RGB and scale pixels to [0,1].
Normalize channels using mean [0.485,0.456,0.406] and std [0.229,0.224,0.225].
Run the CPU ResNet-18 in evaluation mode without gradients.
Apply softmax across six classes and return the top label and confidence.
Return HTTP 500 with error text if processing raises an exception.
```

The endpoint performs no resizing or confidence rejection. Its exact labels are `Adult Spotted Laternfly`, `Early Nypmh Spotted Laternfly`, `Green Leafhopper`, `No Pests`, `Japanese Beetle`, and `Late Nypmh Spotted Laternfly`.

## 4. Web app data access

**Source:** Flutter `mobile_dashboard.dart`, `historical_series_service.dart`, `organization_service.dart`; Functions `getHistoricalSeries.js` and `lib/http.js`.

- **Captures:** Stream the top-level Firestore `captures` collection ordered by descending timestamp, optionally filtering `detected_disease == true`. The query has no organization/site/zone filter or pagination. Individual photo results remain in widget state.
- **History:** Verify the Firebase token and organization membership document; validate required fields; query BigQuery by organization, site, zones, readings, and inclusive time bounds. Aggregate by time bucket and return chart series. Use `sensorId` when supplied, otherwise primary-sensor readings. Flutter converts timestamps to local time and sorts points.
- **Roles:** The Flutter service checks `canManageMembers`, rejects self-role changes, requires an existing target member, and restricts owner changes to owners. It checks the remaining owner count before demotion, then updates role-derived permissions directly in Firestore. Checks and writes are separate, nontransactional operations.

# Build-versus-reuse decisions

# API contract

Each Python service has its own base URL; `getHistoricalSeries` is a Firebase HTTPS function. Image requests use multipart form data; history uses JSON with a Firebase bearer token. The Python handlers have no application-level authentication checks. Responses retain each service's existing field names and structure.

## Endpoints

| Method / route | Input | Success | Errors |
|---|---|---|---|
| `POST /predict` | Required multipart `file`: image. | `200`: `DiseaseResponse`. | `422` for a missing file; processing exceptions have no custom error response. |
| `POST /pests_predict` | Required multipart `file`: image. | `200`: `PestResponse`. | `422` for a missing file; caught exceptions return `500` with `{success:false,error:string}`. |
| `POST /process-capture` | Repeated multipart `files`: TIFFs; optional `capture_id: string`. | `200`: `CaptureResponse` after processing/upload. | `400` for no uploads or fewer than five accepted files. Pipeline exceptions return `500` with `{error:string,capture_id:string}`; file-saving exceptions fall outside this catch. |
| `POST /getHistoricalSeries` | Bearer token; JSON `HistoryRequest`. | `200`: `HistoryResponse`. | `400` invalid/missing required fields; `401` invalid token; `403` absent membership; `500` caught exception. Error body: `{success:false,error:string}`. `OPTIONS`: `204`; other methods: `405`. |
| `GET /health` (photo disease) | None. | `200`: `{"status":"ok"}`. | No custom failure branch. |
| `GET /healthz` (multispectral) | None. | `200`: `{"ok":true}`. | No custom failure branch. |

Multispectral uploads skip unsupported extensions/empty filenames and require at least five saved TIFFs. Files must follow the expected band ordering. The application defines no upload-size limit or duplicate-request protection. The image clients do not implement automatic retries or job polling.

## Data types

`?` marks optional inputs. Types describe the actual payload shapes; they do not imply additional runtime validation.

| Type | Fields |
|---|---|
| `DiseaseResponse` | `disease: string`, `confidence: number`, `top3: {disease: string, confidence: number}[]`. |
| `PestResponse` | `success: true`, `prediction: string`, `confidence: number`. |
| `CaptureResponse` | `capture_id: string`, `aligned_dir: string`, `processed_dir: string`, `inference_dir: string`, `inference_summary: InferenceSummary`, `firebase_upload: UploadSummary`, `cleanup: boolean`. Directory fields are server-local paths. |
| `InferenceSummary` | `device: string`, `disease_detected: boolean`, `analysis_label: integer` (0/1), `max_disease_probability: number`, `vine_positive_tiles: integer`, `disease_positive_tiles: integer`, `output_files: {overlay: string, heatmap: string, boxes: string}` (local paths). |
| `UploadSummary` | `firestore_document: string`, `processed_urls: map<string,string>`, `inference_urls: map<string,string>`. Maps associate filenames with public URLs. |
| `HistoryRequest` | `organizationId: string`, `siteId: string`, `zoneIds: string[]`, `readings: string[]`, `start: string`, `end: string`, `interval?: string`, `sensorId?: string`, `timezone?: string`, `aggregation?: string`. Arrays must be nonempty; dates must be parseable. |
| `HistoryResponse` | `success: true`, `interval: string`, `aggregation: string`, `graphs: Graph[]`. No matching rows returns `graphs: []`. |
| `Graph` | `field: string`, `unit: string\|null`, `series: {zoneId: string, points: {t: string, v: number}[]}[]`. Each point contains a bucket timestamp and aggregated value. |

History intervals are `MINUTE_15`, `MINUTE_30`, `HOUR`, `DAY`, `WEEK`, or `MONTH`. Automatic selection uses `MINUTE_30` for ranges up to three days, `HOUR` up to five days, and `DAY` above five days. Aggregation accepts `AVG`, `MIN`, or `MAX`, defaulting to `AVG`; timezone defaults to `America/New_York`. Unrecognized intervals use automatic selection. The handler checks neither start-before-end nor site/zone parentage and defines no range/point-count limit.

## Direct Firestore operations

| Operation | Contract |
|---|---|
| Save capture | `.set()` on `captures/{capture_id}` writes `capture_id: string`, `timestamp: server timestamp`, `raw_folder_name: string`, `detected_disease: boolean`, `analysis: InferenceSummary`, and the two URL maps. Existing contents are replaced. No organization/site/zone fields are written. Failures propagate to the pipeline handler. |
| Stream captures | `.snapshots()` returns live documents using the filter/order described in Core algorithms. The dashboard shows loading/empty states but has no explicit `snapshot.hasError` branch. |
| Update role | `updateMemberRole(orgId: String, userId: String, newRole: String)` returns `Future<void>` and updates `role` plus boolean permissions `canManageMembers`, `canManageSites`, and `canManageSensors`. Failed checks throw exceptions; SDK errors propagate. Owners/admins receive all permissions, members receive site/sensor permissions, and viewers receive none. |

## Example payload

For `POST /pests_predict`, send a multipart part named `file` containing the image. An illustrative `200` response is:

```json
{
  "success": true,
  "prediction": "Japanese Beetle",
  "confidence": 0.93
}
```

A caught processing exception instead returns `500` with `{"success":false,"error":"<exception text>"}`. Flutter displays the prediction on success or an error message on failure.

# Technology choices with justification