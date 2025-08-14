# AI Podcast Generator (Internal Deployment)

## Project Overview

The AI Podcast Generator is an innovative internal solution designed to rapidly create personalized podcasts from diverse content sources. This system addresses the need for efficient internal content production while maintaining strict data security and intellectual property protection.

Based on the public open source [Podcastfy.ai](https://github.com/souzatharsis/podcastfy) project which focuses on open source, programmatic and bespoke generation of engaging, conversational content, our internal deployment is specifically designed to:

- Keep all data processing within our secure datacenter environment
- Eliminate reliance on external cloud-based AI services
- Provide a secure platform for transforming internal information into high-quality audio podcasts
- Maintain full control over sensitive organizational data

## Key Features

### Content Generation
- **Text-to-Podcast Generation**: Convert internal documents, reports, and text content into engaging audio podcasts
- **Audio-to-Podcast Generation**: Transform meeting recordings and other spoken-word audio into structured podcast content
- **Multi-Modal Support**: Process various content types including text documents, images, and audio files

### Security & Compliance
- **On-Premises Processing**: All AI processing occurs within our internal datacenter
- **Zero Data Exposure**: No sensitive internal data is sent to external services
- **Intellectual Property Protection**: Maintain complete control over generated content

### User Experience
- **Web-Based Interface**: Intuitive web application for content submission and podcast management
- **Processing Progress Visualization**: Real-time feedback on podcast generation status
- **Pre-Designed Templates**: Streamlined content creation with customizable podcast formats

## Technical Architecture

### Core Components
- **Podcastfy Backend**: Extended version of the open-source Podcastfy framework
- **Whisper ASR Service**: For speech-to-text transcription of audio inputs
- **Kyutai TTS**: Advanced text-to-speech synthesis for natural-sounding voices
- **Ollama LLM**: Local large language model for content processing and conversation generation

### Deployment
- **Containerized Services**: All components deployed via Docker and Docker Compose
- **GPU Acceleration**: Leveraging local gaming PC GPU for accelerated AI processing
- **Microservices Architecture**: Clearly defined APIs between all system components

## Usage Examples

### Text-to-Podcast Workflow
1. Access the web interface
2. Submit text content (documents, reports, notes)
3. Select a podcast template (interview, storytelling, news update)
4. Monitor processing progress in real-time
5. Download or stream the generated podcast

### Audio-to-Podcast Workflow
1. Upload meeting audio or spoken-word content
2. System automatically transcribes audio using Whisper ASR
3. Ollama LLM processes transcript into engaging conversation format
4. Kyutai TTS generates high-quality audio output
5. Access final podcast through web interface

## Customization Options

### Podcast Templates
- Interview-style discussions
- News updates and briefings
- Educational content delivery
- Storytelling formats
- Executive summaries

### Voice Customization
- Multiple speaker configurations
- Emotional tone adjustments
- Language and accent preferences
- Pacing and emphasis controls

## Security Benefits

### Data Protection
- No data leaves the internal network during processing
- All AI models run locally on our hardware
- Full compliance with internal data security policies
- Elimination of third-party data handling risks

### Intellectual Property Control
- Complete ownership of generated content
- No exposure to external AI training datasets
- Protection of proprietary information and terminology
- Controlled access to generated podcasts

## System Requirements

### Hardware
- Gaming PC with dedicated GPU (8GB+ VRAM recommended)
- Docker and Docker Compose installed
- NVIDIA Container Toolkit for GPU support

### Software
- Linux-based operating system
- Python 3.11+
- Docker Engine
- Required AI model files and dependencies

## Getting Started

### Initial Setup
1. Clone the internal repository
2. Configure Docker Compose environment
3. Install required dependencies
4. Set up local AI models
5. Launch services via Docker Compose

### Web Interface Access
After deployment, access the web interface at `http://localhost:3000` (or configured internal address).

## Future Enhancements

### Planned Features
- Real-time visual voice feedback
- Advanced content curation and editing tools
- Integration with internal knowledge bases
- Multi-user authentication and access controls

### Long-term Vision
Establish the AI Podcast Generator as the primary internal platform for on-demand audio content creation, transforming how internal communications, training, and knowledge sharing are conducted.

## Contributing

This internal deployment is maintained by our organization's AI and software development teams. For access or contribution requests, please contact the project maintainers.