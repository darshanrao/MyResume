# B-Roll Extraction: Scalable AI-Powered Video Retrieval - STAR Analysis

## **SITUATION**
High-quality stock footage is expensive, with costs reaching up to $120 per clip, creating a significant barrier for scalable content production, especially for creators, LLM-native video workflows, and automated content pipelines. There was a need for a cost-effective, automated solution to extract and rank B-roll footage from freely available sources like YouTube, without sacrificing quality or relevance.

## **TASK**
The objective was to design and implement an AI-powered pipeline that:
- Accepts a raw user query (e.g., "beach sunset") and reformulates it for optimal search results
- Searches YouTube for the most relevant, royalty-free B-roll footage
- Extracts and ranks high-quality 3–6 second video clips based on subject relevance, technical quality, composition, and aesthetics
- Achieves up to 99% cost savings compared to traditional stock libraries
- Enables scalable, automated, and creative video content production

## **ACTION**

### **Pipeline Implementation:**

#### **1. Query Enhancement & Search:**
- Accepts user input and programmatically augments the query:
  - Prefix: "B-roll footage royalty-free"
  - Suffix: "4K"
  - Example: "B-roll footage royalty-free beach sunset 4K"
- Uses the YouTube API to search for the top 6 video matches
- Limits extraction to a maximum of 30 seconds per video for efficiency

#### **2. Frame Sampling & Multimodal Analysis:**
- Samples video frames at 1 FPS for efficient processing
- For each frame, uses OpenAI Vision (GPT-4o) with a detailed multimodal prompt:
  - Evaluates subject presence, action quality, technical quality, composition, aesthetics, and obstructions
  - Returns a structured JSON response for each frame

#### **3. Clip Selection & Scoring:**
- Applies a sliding window to identify 3–6 second segments with the highest density of positive detections
- Each candidate clip is scored based on the average of technical_quality and composition_quality (out of 10)
- Processes clips in batches of three:
  - Selects the highest-scoring clip from each batch
  - If the score exceeds a threshold (currently 8), the clip is extracted and the process ends
  - Otherwise, the next batch is analyzed until a suitable clip is found or all options are exhausted
- Prioritizes longer, continuous detections for better B-roll quality

#### **4. Extraction:**
- Uses ffmpeg to extract the selected high-quality segment from the source video

#### **5. Cost Optimization:**
- Sets OpenAI Vision detail to "low" for cost efficiency
- Analyzes only necessary frames and text, minimizing token usage
- Achieves up to 99% cost savings compared to purchasing stock footage

### **Technology Stack:**
- **Python**: Core scripting and orchestration
- **YouTube API**: Video search and retrieval
- **OpenAI Vision (GPT-4o)**: Multimodal frame analysis and scoring
- **ffmpeg**: Video segment extraction

### **Key Features Implemented:**
1. **Automated Query Augmentation**: Ensures optimal search results for B-roll
2. **Multimodal Frame Analysis**: Uses advanced AI to assess frame suitability
3. **Adaptive Clip Ranking**: Ranks and selects clips based on multiple quality metrics
4. **Batch Processing**: Efficiently processes and selects the best clips
5. **Cost Control**: Fine-tuned for minimal API and compute costs

## **RESULT**

### **Technical Achievements:**
- **Scalable Pipeline**: Processes up to 12 videos and hundreds of frames per query
- **High-Quality Output**: Consistently extracts visually appealing, relevant B-roll clips
- **Cost Efficiency**: Reduces per-clip cost from ~$120 to as low as $0.15–$0.60
- **Automation**: Enables fully automated, repeatable B-roll extraction for any query

### **Performance & Cost Metrics:**
| Scenario         | Videos Processed | Frames Analyzed | Total Cost | Savings vs. Worst Case |
|------------------|------------------|-----------------|-----------|-----------------------|
| Best Case        | 3                | 90              | $0.15     | 75%                   |
| Average Case     | 6                | 180             | $0.30     | 50%                   |
| Worst Case       | 12               | 360             | $0.60     | Baseline              |

- **Token Usage**: Calculated based on image, text input, and text output tokens per frame
- **Adaptive Ranking**: Ensures only the highest-quality clips are selected

### **User & Business Impact:**
- **99% Cost Savings**: Makes high-quality B-roll accessible for all creators
- **Scalability**: Supports large-scale, automated content production
- **Creative Enablement**: Empowers LLM-native workflows and creative tooling
- **No Licensing Barriers**: Extracts from royalty-free sources, reducing legal risk

### **Innovation Highlights:**
- **Multimodal AI Evaluation**: Leverages GPT-4o for nuanced, context-aware frame analysis
- **Sliding Window & Batch Strategy**: Maximizes the chance of finding optimal clips quickly
- **Adaptive Query & Prompt Engineering**: Continuously improves search and analysis quality
- **Open-Source & Extensible**: Built with widely available tools and APIs

---

## **Technical Architecture Overview**

### **Core Components:**
1. **Query Augmentation & Search**
   - User query enhancement
   - YouTube API integration
2. **Frame Sampling & Analysis**
   - 1 FPS frame extraction
   - OpenAI Vision multimodal prompt evaluation
3. **Clip Selection & Ranking**
   - Sliding window for 3–6s segments
   - Quality scoring and adaptive thresholding
   - Batch processing for efficiency
4. **Extraction**
   - ffmpeg-based video segment extraction

### **Data Flow:**
1. **User Query** → Query Augmentation → YouTube Search
2. **Video Download** → Frame Sampling (1 FPS)
3. **Frame Analysis** → JSON Scoring via OpenAI Vision
4. **Sliding Window** → Clip Selection & Ranking
5. **ffmpeg Extraction** → Final B-roll Output

### **API Integrations:**
- **YouTube API**: Video search and download
- **OpenAI Vision (GPT-4o)**: Multimodal frame analysis
- **ffmpeg**: Video segment extraction

### **Reference & Cost Calculation:**
- [OpenAI Vision Documentation](https://platform.openai.com/docs/guides/vision)
- Cost calculated based on token usage for images, text input, and output
- Adaptive ranking and batch processing minimize unnecessary API calls and costs 
