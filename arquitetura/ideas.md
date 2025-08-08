# Fullstack AI Application Ideas

## 1. AI-Powered Content Moderation Platform

AI-driven platform for real-time content moderation across text, images, and video uploads with customizable policy enforcement.

Extends the stack with Redis for queue management and background processing of moderation tasks to handle large content volumes without blocking the main thread.

### Deploy

```yml
version: '3.8'

services:
  frontend:
    build: ./frontend
    ports:
      - "3000:3000"
    depends_on:
      - backend
    networks:
      - moderation-network
  backend:
    build: ./backend
    ports:
      - "8000:8000"
    volumes:
      - ./backend:/app
    environment:
      - OPENAI_API_KEY=${OPENAI_API_KEY}
      - MONGODB_URL=${MONGODB_URL}
      - REDIS_URL=${REDIS_URL}
    networks:
      - moderation-network
    restart: unless-stopped
  redis:
    image: redis:alpine
    ports:
      - "6379:6379"
    networks:
      - moderation-network

networks:
  moderation-network:
    driver: bridge
```

### Backend

```py
# Redis-based task queue for content moderation
from redis import Redis

redis = Redis(host="redis", port=6379, db=0)

class ModerationQueue:
    def add_task(self, content_id, content_data):
        self.redis.hset(f"moderation:{content_id}", mapping={
            "status": "queued",
            "content": json.dumps(content_data),
            "timestamp": time.time()
        })
        self.redis.lpush("moderation_queue", content_id)

@app.post("/api/moderate")
async def moderate_content(content: ContentRequest, background_tasks: BackgroundTasks):
    content_id = generate_id()
    queue = ModerationQueue()
    
    # Add to Redis queue instead of direct processing
    queue.add_task(content_id, {
        "text": content.text,
        "media_url": content.media_url,
        "policy_level": content.policy_level
    })
    
    # Start worker if needed
    background_tasks.add_task(ensure_worker_running)
```

### Frontend

```tsx
// Real-time moderation status updates using WebSockets
import { useWebSocket } from '../hooks/useWebSocket'

// WebSocket connection for real-time updates
const socket = useWebSocket('ws://localhost:8000/ws/moderation')

useEffect(() => {
  if (!socket || !contentId) return
  
  // Listen for updates on this specific content ID
  socket.send(JSON.stringify({ action: 'subscribe', content_id: contentId }))
  
  socket.addEventListener('message', (event) => {
    const data = JSON.parse(event.data)
    if (data.content_id === contentId) {
      setStatus(data.status)
      if (data.status === 'complete') {
        setResult(data.result)
      }
    }
  })
  
  return () => {
    socket.send(JSON.stringify({ action: 'unsubscribe', content_id: contentId }))
  }
}, [socket, contentId])

  return (
    <div className="p-6 max-w-4xl mx-auto bg-white rounded-xl shadow-md">
      <h2 className="text-2xl font-bold mb-4">Content Moderation</h2>
      
      <form onSubmit={handleSubmit}>
        <textarea 
          className="w-full p-2 border rounded" 
          value={content}
          onChange={(e) => setContent(e.target.value)}
          placeholder="Enter content to moderate"
        />
        
        <div className="mt-4 flex items-center gap-4">
          <button 
            type="submit"
            className="px-4 py-2 bg-blue-600 text-white rounded"
            disabled={status !== 'idle'}
          >
            Submit for Moderation
          </button>
          
          {status !== 'idle' && (
            <div className="flex items-center">
              <div className="animate-pulse h-3 w-3 bg-blue-500 rounded-full mr-2"></div>
              <span className="text-sm text-gray-600">
                {status === 'queued' && 'Queued for moderation...'}
                {status === 'processing' && 'Processing content...'}
                {status === 'complete' && 'Moderation complete'}
              </span>
            </div>
          )}
        </div>
      </form>
      
      {result && (
        <div className="mt-6 p-4 border rounded">
          <div className={`text-lg font-bold ${result.safe ? 'text-green-600' : 'text-red-600'}`}>
            {result.safe ? 'Content Approved' : 'Content Flagged'}
          </div>
          <p>Confidence: {result.confidence.toFixed(2)}%</p>
          <p>Explanation: {result.explanation}</p>
        </div>
      )}
    </div>
  )
}
```

## 2. AI Document Analysis and Extraction Platform

AI-powered platform for extracting structured data from documents, with automated classification, data extraction, and validation workflows.

Uses MongoDB for document storage, OCR for text extraction, and implements a multi-stage processing pipeline for asynchronous document analysis with progress tracking.

### Deploy

```yml
version: '3.8'

services:
  frontend:
    build: ./frontend
    ports:
      - "3000:3000"
    depends_on:
      - backend
    networks:
      - docai-network
  backend:
    build: ./backend
    ports:
      - "8000:8000"
    volumes:
      - ./backend:/app
      - ./storage:/storage
    environment:
      - OPENAI_API_KEY=${OPENAI_API_KEY}
      - MONGODB_URL=${MONGODB_URL}
      - STORAGE_PATH=/storage
    networks:
      - docai-network
    restart: unless-stopped
  mongodb:
    image: mongo:latest
    ports:
      - "27017:27017"
    volumes:
      - mongodb_data:/data/db
    networks:
      - docai-network

volumes:
  mongodb_data:

networks:
  docai-network:
    driver: bridge
```

### Backend

```py
# Multi-stage document processing pipeline with MongoDB integration
from motor.motor_asyncio import AsyncIOMotorClient
import asyncio

# MongoDB connection setup
db_client = AsyncIOMotorClient(os.getenv("MONGODB_URL"))
db = db_client.document_db

# Document processing pipeline stages
class DocumentPipeline:
    @staticmethod
    async def preprocess(document_id):
        # Update document status
        await db.documents.update_one(
            {"_id": document_id},
            {"$set": {"status": "preprocessing"}}
        )
        
        # Get document from storage
        doc = await db.documents.find_one({"_id": document_id})
        
        # Process the document with OCR
        text, pages = await extract_text_with_ocr(doc["file_path"])
        
        # Update with extracted text
        await db.documents.update_one(
            {"_id": document_id},
            {"$set": {
                "raw_text": text,
                "pages": pages,
                "status": "extracted"
            }}
        )
        
        # Move to next stage
        asyncio.create_task(DocumentPipeline.analyze(document_id))
    
    @staticmethod
    async def analyze(document_id):
        # Use AI to analyze document type and extract fields
        doc = await db.documents.find_one({"_id": document_id})
        analysis_result = await analyze_with_ai(doc["raw_text"])
        
        # Update with analysis results
        await db.documents.update_one(
            {"_id": document_id},
            {"$set": {
                "document_type": analysis_result.document_type,
                "fields": analysis_result.fields,
                "status": "completed"
            }}
        )
```

### Frontend

```tsx
// Document processing status tracking and preview
import { useDocumentPolling } from '../hooks/useDocumentPolling'

// Document processing visualization
const renderProcessingSteps = () => {
  const steps = [
    { id: 'uploaded', label: 'Uploaded' },
    { id: 'preprocessing', label: 'Preprocessing' },
    { id: 'extracted', label: 'Text Extracted' },
    { id: 'analyzing', label: 'Analyzing' },
    { id: 'completed', label: 'Completed' }
  ]
  
  const currentStepIndex = steps.findIndex(step => step.id === status)
  
  return (
    <div className="mb-8">
      <div className="flex items-center justify-between w-full">
        {steps.map((step, index) => (
          <div key={step.id} className="flex flex-col items-center">
            <div 
              className={`w-8 h-8 rounded-full flex items-center justify-center
                ${index <= currentStepIndex ? 'bg-blue-500 text-white' : 'bg-gray-200'}`}
            >
              {index < currentStepIndex ? '✓' : index + 1}
            </div>
            <span className="text-xs mt-1">{step.label}</span>
          </div>
        ))}
      </div>
      
      <div className="relative mt-2">
        <div className="absolute top-0 left-0 h-1 bg-gray-200 w-full"></div>
        <div 
          className="absolute top-0 left-0 h-1 bg-blue-500 transition-all duration-500"
          style={{ width: `${(currentStepIndex / (steps.length - 1)) * 100}%` }}
        ></div>
      </div>
    </div>
  )
}

// Document preview with extracted data display
const renderExtractedData = () => (
  <div className="border rounded-lg p-4">
    <h3 className="text-lg font-medium mb-4">Extracted Data</h3>
    
    {document.document_type && (
      <div className="mb-4">
        <span className="text-sm font-medium text-gray-500">Document Type:</span>
        <span className="ml-2 px-2 py-1 bg-blue-100 text-blue-800 rounded text-sm">
          {document.document_type}
        </span>
      </div>
    )}
    
    {document.fields?.map((field, index) => (
      <div key={index} className="border-b pb-3 last:border-b-0">
        <div className="flex justify-between items-center">
          <span className="font-medium">{field.name}</span>
          <span className="text-xs bg-gray-100 px-2 py-1 rounded">{field.type}</span>
        </div>
        <p className="mt-1 text-gray-700">{field.value}</p>
        <div className="mt-1 flex items-center">
          <span className="text-xs text-gray-500 mr-1">Confidence:</span>
          <div className="w-24 h-2 bg-gray-200 rounded-full overflow-hidden">
            <div 
              className="h-full bg-green-500" 
              style={{ width: `${field.confidence * 100}%` }}
            ></div>
          </div>
        </div>
      </div>
    ))}
  </div>
)

// Document preview with page navigation
const renderDocumentPreview = () => {
  if (!document?.pages || document.pages.length === 0) {
    return <div className="text-center py-10">No preview available</div>
  }
  
  return (
    <div className="border rounded overflow-hidden">
      <div className="flex justify-between p-2 bg-gray-100 border-b">
        <span>Page {document.currentPage} of {document.pages.length}</span>
        <div>
          <button 
            onClick={() => document.prevPage()} 
            disabled={document.currentPage === 1}
            className="px-2 py-1 mr-2 bg-white border rounded"
          >
            Previous
          </button>
          <button 
            onClick={() => document.nextPage()} 
            disabled={document.currentPage === document.pages.length}
            className="px-2 py-1 bg-white border rounded"
          >
            Next
          </button>
        </div>
      </div>
      <div className="p-4 bg-gray-50 min-h-[500px] flex justify-center">
        <img 
          src={document.pages[document.currentPage - 1].imageUrl} 
          alt={`Page ${document.currentPage}`}
          className="max-w-full max-h-[500px] object-contain"
        />
      </div>
    </div>
  )
}

// Extracted data visualization
const renderExtractedData = () => {
  if (!document?.fields || document.fields.length === 0) {
    return <div className="text-center py-10">No data extracted yet</div>
  }
  
  return (
    <div className="border rounded overflow-hidden">
      <div className="p-4">
        <h3 className="text-lg font-medium mb-2">Document Type: {document.document_type}</h3>
        <div className="grid grid-cols-2 gap-4">
          {document.fields.map((field, index) => (
            <div key={index} className="border rounded p-3">
              <div className="text-sm text-gray-500">{field.name}</div>
              <div className="font-medium">{field.value}</div>
              <div className="mt-1 flex items-center">
                <div 
                  className="h-1 bg-green-500 rounded" 
                  style={{ width: `${field.confidence * 100}%` }}
                />
                <span className="ml-2 text-xs">{(field.confidence * 100).toFixed(0)}%</span>
              </div>
            </div>
          ))}
        </div>
      </div>
    </div>
  )
}

const DocumentTracker = () => {
  const { documentId } = useParams<{ documentId: string }>()
  const { document, status, progress } = useDocumentPolling(documentId)
  const [activeTab, setActiveTab] = useState<'preview'|'data'|'raw'>('preview')
  
  if (!document) {
    return <div className="text-center py-10">Loading document...</div>
  }
  
  return (
    <div className="max-w-4xl mx-auto p-6">
      <h1 className="text-2xl font-bold mb-2">{document.original_filename}</h1>
      <p className="text-gray-500 mb-6">Uploaded {new Date(document.created_at).toLocaleString()}</p>
      
      {renderProcessingSteps()}
      
      <div className="mb-4 border-b">
        <div className="flex">
          <button
            className={`px-4 py-2 ${activeTab === 'preview' ? 'border-b-2 border-blue-500' : ''}`}
            onClick={() => setActiveTab('preview')}
          >
            Document Preview
          </button>
          <button
            className={`px-4 py-2 ${activeTab === 'data' ? 'border-b-2 border-blue-500' : ''}`}
            onClick={() => setActiveTab('data')}
          >
            Extracted Data
          </button>
          <button
            className={`px-4 py-2 ${activeTab === 'raw' ? 'border-b-2 border-blue-500' : ''}`}
            onClick={() => setActiveTab('raw')}
          >
            Raw Text
          </button>
        </div>
      </div>
      
      {activeTab === 'preview' && renderDocumentPreview()}
      {activeTab === 'data' && renderExtractedData()}
      {activeTab === 'raw' && (
        <div className="border rounded p-4 bg-gray-50 font-mono text-sm whitespace-pre-wrap">
          {document.raw_text || 'No text extracted yet'}
        </div>
      )}
    </div>
  )
}
```

## 3. AI-Powered Personalized Learning Platform

Adaptive learning platform that uses AI to create personalized educational content and assessments based on individual learning patterns.

Leverages PostgreSQL for relational data storage with knowledge graphs for concept relationships, and implements adaptive content generation based on learning styles and knowledge levels.

### Deploy

```yml
version: '3.8'

services:
  frontend:
    build: ./frontend
    ports:
      - "3000:3000"
    depends_on:
      - backend
    networks:
      - learning-network
  backend:
    build: ./backend
    ports:
      - "8000:8000"
    volumes:
      - ./backend:/app
    environment:
      - OPENAI_API_KEY=${OPENAI_API_KEY}
      - POSTGRES_URL=${POSTGRES_URL}
    networks:
      - learning-network
    restart: unless-stopped
  postgres:
    image: postgres:14
    ports:
      - "5432:5432"
    environment:
      - POSTGRES_PASSWORD=${POSTGRES_PASSWORD}
      - POSTGRES_USER=${POSTGRES_USER}
      - POSTGRES_DB=${POSTGRES_DB}
    volumes:
      - postgres_data:/var/lib/postgresql/data
    networks:
      - learning-network

volumes:
  postgres_data:

networks:
  learning-network:
    driver: bridge
```

### Backend

```py
# Knowledge graph for learning path generation
import networkx as nx

class KnowledgeGraph:
    def __init__(self):
        self.graph = nx.DiGraph()
        self.load_graph()
    
    def load_graph(self):
        # Load concept relationships from database
        concepts = db.query(Concept).all()
        for concept in concepts:
            self.graph.add_node(concept.id, name=concept.name, difficulty=concept.difficulty)
        
        # Add prerequisite relationships as directed edges
        relationships = db.query(ConceptRelationship).all()
        for rel in relationships:
            self.graph.add_edge(
                rel.prerequisite_id, 
                rel.concept_id, 
                weight=rel.strength
            )
    
    def get_learning_path(self, target_concept_id, user_knowledge):
        # Find optimal learning path based on user's current knowledge
        path = []
        visited_concepts = set()
        
        # Start from concepts user already knows
        frontier = [concept_id for concept_id, level in user_knowledge.items() if level > 0.7]
        
        while frontier:
            current = frontier.pop(0)
            if current in visited_concepts:
                continue
                
            visited_concepts.add(current)
            path.append(current)
            
            if current == target_concept_id:
                break
                
            # Get neighbors sorted by prerequisite relationship strength
            neighbors = sorted(
                self.graph.successors(current),
                key=lambda n: self.graph[current][n]['weight'],
                reverse=True
            )
            
            frontier.extend(n for n in neighbors if n not in visited_concepts)
        
        return path

# Learning style adaptation
class AdaptiveContentGenerator:
    async def _generate_styled_content(self, concept, difficulty, learning_style):
        # Adapt content format based on primary learning style
        style_prompts = {
            "visual": "Use diagrams, charts, and visual metaphors",
            "auditory": "Include dialogues, discussions, and spoken explanations",
            "reading": "Provide detailed written explanations with examples",
            "kinesthetic": "Include interactive exercises and practical applications"
        }
        
        # Build prompt based on user's learning style preferences
        primary_style = learning_style.primary_style
        primary_prompt = style_prompts[primary_style]
        
        # Get secondary style
        style_scores = {
            "visual": learning_style.visual_score,
            "auditory": learning_style.auditory_score,
            "reading": learning_style.reading_score,
            "kinesthetic": learning_style.kinesthetic_score
        }
        
        # Generate content with AI
        prompt = f"Create educational content about {concept.name} at difficulty level {difficulty*10}/10.\n"
        prompt += f"Primary focus: {primary_prompt}.\n"
        prompt += "Include 3-5 assessment questions that test understanding."
        
        result = await self.ai_agent.arun([prompt])
        return result.data
```

### Frontend

```tsx
// Interactive learning path visualization with adaptive content delivery
import { useLearningPath } from '../hooks/useLearningPath'

// Learning path visualization with knowledge graph
const renderLearningMap = () => {
  return (
    <div className="relative py-10">
      {/* Path visualization with nodes and connections */}
      <svg className="w-full h-full" style={{ position: 'absolute', zIndex: 0 }}>
        {learningPath.connections.map((connection, idx) => (
          <line 
            key={idx}
            x1={connection.x1} 
            y1={connection.y1} 
            x2={connection.x2} 
            y2={connection.y2}
            stroke="#94a3b8"
            strokeWidth="2"
            strokeDasharray={connection.completed ? undefined : "5,5"}
          />
        ))}
      </svg>
      
      {/* Concept nodes */}
      <div className="relative z-10 flex flex-wrap justify-center gap-4">
        {learningPath.concepts.map((concept) => {
          const conceptProgress = progress[concept.id] || 0
          const isCompleted = conceptProgress >= 1
          const isCurrent = currentConcept?.id === concept.id
          const isAvailable = concept.available
          
          return (
            <div 
              id={`node-${concept.id}`}
              key={concept.id}
              className={`
                w-48 p-4 rounded-lg shadow-md transition-all
                ${isCompleted ? 'bg-green-100 border-green-500' : ''}
                ${isCurrent ? 'ring-2 ring-blue-500 transform scale-110' : ''}
                ${!isAvailable ? 'opacity-50' : ''}
              `}
            >
              <h3 className="font-medium text-gray-900">{concept.name}</h3>
              
              {/* Progress indicator */}
              <div className="mt-3 h-2 w-full bg-gray-200 rounded-full overflow-hidden">
                <div 
                  className="h-full bg-green-500 transition-all duration-500" 
                  style={{ width: `${conceptProgress * 100}%` }}
                />
              </div>
              
              {/* Status indicator */}
              <div className="mt-2 flex items-center justify-between text-xs">
                <span>
                  {isCompleted && '✓ Completed'}
                  {isCurrent && '➤ Current'}
                  {!isCompleted && !isCurrent && isAvailable && 'Available'}
                  {!isAvailable && 'Locked'}
                </span>
                <span>{Math.round(conceptProgress * 100)}%</span>
              </div>
            </div>
          )
        })}
      </div>
    </div>
  )
}

// Adaptive content based on learning style
const renderAdaptiveContent = () => (
  <div className="prose max-w-none">
    {currentConcept.content ? (
      <div dangerouslySetInnerHTML={{ __html: currentConcept.content }} />
    ) : (
      <div className="animate-pulse">
        <div className="h-6 bg-gray-200 rounded w-3/4 mb-4"></div>
        <div className="h-4 bg-gray-200 rounded w-full mb-2"></div>
        <div className="h-4 bg-gray-200 rounded w-5/6 mb-2"></div>
      </div>
    )}
    
    {/* Assessment section */}
    {currentConcept.assessment && (
      <div className="mt-10 border-t pt-6">
        <h3 className="text-xl font-semibold mb-4">Check Your Understanding</h3>
        <div className="space-y-6">
          {currentConcept.assessment.questions.map((question, idx) => (
            <div key={idx} className="border rounded-lg p-4">
              <p className="font-medium mb-3">{question.text}</p>
              <div className="space-y-2">
                {question.options.map((option, optIdx) => (
                  <div 
                    key={optIdx}
                    className={`p-3 border rounded ${question.selectedOption === optIdx ? 'bg-blue-50 border-blue-300' : ''}`}
                  >
                    {option}
                  </div>
                ))}
              </div>
            </div>
          ))}
        </div>
      </div>
    )
  }
  
  return (
    <div className="container mx-auto p-6">
      <h1 className="text-3xl font-bold mb-2">{learningPath?.title || 'Learning Path'}</h1>
      <p className="text-gray-600 mb-6">{learningPath?.description}</p>
      
      <div className="mb-6 flex justify-between items-center">
        <div className="flex gap-4">
          <button
            className={`px-4 py-2 rounded ${viewMode === 'map' ? 'bg-blue-100 text-blue-800' : 'bg-gray-100'}`}
            onClick={() => setViewMode('map')}
          >
            Learning Map
          </button>
          <button
            className={`px-4 py-2 rounded ${viewMode === 'content' ? 'bg-blue-100 text-blue-800' : 'bg-gray-100'}`}
            onClick={() => setViewMode('content')}
            disabled={!currentConcept}
          >
            Current Concept
          </button>
        </div>
        
        <div className="text-sm text-gray-600">
          Overall progress: {Math.round(learningPath?.overall_progress * 100)}%
        </div>
      </div>
      
      {viewMode === 'map' ? renderLearningMap() : renderConceptContent()}
    </div>
  )
}
```