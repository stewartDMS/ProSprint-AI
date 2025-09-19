
# ProSprint AI Document Builder - Download & Functionality Fix Plan

## Analysis Summary

After analyzing the codebase, I've identified several critical issues preventing the document builder from functioning properly:

## 🔍 Key Issues Identified

### 1. **Missing Document Export API Endpoints**
- The document export functionality references `/api/documents/export` but this endpoint doesn't exist in the server routes
- The `documentExport.ts` service exists but isn't connected to the API routes

### 2. **Incomplete Document Management API**
- Missing `/api/documents` POST endpoint for saving documents
- Missing `/api/documents` GET endpoint for fetching user documents
- No connection between document generation and storage

### 3. **Frontend-Backend Disconnect**
- Document builder generates content but can't save it
- Export buttons trigger API calls to non-existent endpoints
- No proper error handling for failed API calls

### 4. **Edit/Delete Functionality Issues**
- Section editing works in local state but changes aren't persisted
- Delete section functionality exists but doesn't save changes
- No API endpoints to update document sections

### 5. **Missing Database Schema**
- No document table in the database schema
- Document sections aren't properly structured for storage

## 🛠️ Fix Plan

### Phase 1: Database Schema (Priority: High)
1. Add documents table to database schema
2. Add document_sections table for flexible content storage
3. Update migrations

### Phase 2: Backend API Implementation (Priority: High)
1. Create document management endpoints:
   - `POST /api/documents` - Save document
   - `GET /api/documents` - List user documents
   - `PUT /api/documents/:id` - Update document
   - `DELETE /api/documents/:id` - Delete document
   - `POST /api/documents/export` - Export document
   - `POST /api/documents/generate` - Generate AI content

### Phase 3: Export Service Integration (Priority: High)
1. Connect existing `documentExport.ts` to API routes
2. Implement proper file generation and download
3. Add export format support (PDF, Word, PowerPoint)

### Phase 4: Frontend State Management (Priority: Medium)
1. Fix document saving workflow
2. Implement proper error handling
3. Add loading states for all operations
4. Fix section editing persistence

### Phase 5: UI/UX Improvements (Priority: Low)
1. Add confirmation dialogs for delete operations
2. Improve drag-and-drop visual feedback
3. Add undo/redo functionality

## 🚨 Critical Fixes Needed

### 1. Server Routes Missing
```typescript
// Missing from server/routes.ts
app.post('/api/documents', saveDocument);
app.get('/api/documents', getDocuments);
app.post('/api/documents/export', exportDocument);
app.post('/api/documents/generate', generateDocument);
```

### 2. Database Schema Extension
```sql
-- Missing tables
CREATE TABLE documents (
  id INTEGER PRIMARY KEY,
  title TEXT NOT NULL,
  document_type TEXT NOT NULL,
  user_id TEXT NOT NULL,
  project_id INTEGER,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE document_sections (
  id INTEGER PRIMARY KEY,
  document_id INTEGER NOT NULL,
  title TEXT NOT NULL,
  content TEXT NOT NULL,
  section_type TEXT DEFAULT 'text',
  order_index INTEGER NOT NULL,
  FOREIGN KEY (document_id) REFERENCES documents(id)
);
```

### 3. Export Function Connection
The `documentExport.ts` service exists but isn't connected to any routes.

## 🔧 Implementation Order

1. **First**: Fix database schema and add document storage
2. **Second**: Implement document management API endpoints
3. **Third**: Connect export functionality to routes
4. **Fourth**: Fix frontend state management and persistence
5. **Fifth**: Add proper error handling and user feedback

## 📋 Files That Need Changes

### Backend Files:
- `server/routes.ts` - Add document management routes
- `shared/schema.ts` - Add document and section schemas
- `migrations/` - Add new migration for document tables
- `server/db.ts` - Add document queries

### Frontend Files:
- `client/src/pages/document-builder.tsx` - Fix API calls and state management
- `client/src/pages/report-assistant.tsx` - Fix document management integration

## 🎯 Expected Outcomes

After implementing these fixes:
- ✅ Documents can be saved and retrieved
- ✅ Export functionality works for PDF, Word, and PowerPoint
- ✅ Edit and delete operations persist changes
- ✅ Proper error handling and user feedback
- ✅ Drag and drop reordering saves automatically

## 🚀 Next Steps

1. Implement database schema changes
2. Add missing API endpoints
3. Connect export service to routes
4. Fix frontend state management
5. Test all functionality thoroughly

The main issue is that the frontend assumes backend endpoints exist, but they're not implemented. The document builder UI is well-designed but lacks the backend infrastructure to function properly.
