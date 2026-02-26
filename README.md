/**
 * Ayden AI - Q&A Chatbot Core Logic
 * Main chatbot engine for processing questions and generating responses
 */

class AydenAI {
  constructor(knowledgeBase = {}) {
    this.knowledgeBase = knowledgeBase;
    this.conversationHistory = [];
    this.name = "Ayden AI";
  }

  /**
   * Process user input and generate a response
   * @param {string} userInput - The user's question
   * @returns {string} - The chatbot's response
   */
  respond(userInput) {
    if (!userInput || userInput.trim().length === 0) {
      return "Please ask me a question.";
    }

    const cleanInput = userInput.toLowerCase().trim();
    
    // Store in conversation history
    this.conversationHistory.push({
      user: userInput,
      timestamp: new Date(),
    });

    // Find matching answer
    const answer = this.findAnswer(cleanInput);
    
    // Store bot response in history
    this.conversationHistory.push({
      bot: answer,
      timestamp: new Date(),
    });

    return answer;
  }

  /**
   * Find answer from knowledge base
   * @param {string} query - Cleaned user query
   * @returns {string} - Response from knowledge base or default message
   */
  findAnswer(query) {
    // Exact match
    if (this.knowledgeBase[query]) {
      return this.knowledgeBase[query];
    }

    // Partial match - check if query contains any keywords
    for (const [key, value] of Object.entries(this.knowledgeBase)) {
      if (this.calculateSimilarity(query, key) > 0.6) {
        return value;
      }
    }

    // No match found
    return `I'm not sure about that. Could you ask me something else? My knowledge includes: ${Object.keys(this.knowledgeBase).slice(0, 3).join(", ")}...`;
  }

  /**
   * Calculate similarity between two strings (simple implementation)
   * @param {string} str1 - First string
   * @param {string} str2 - Second string
   * @returns {number} - Similarity score between 0 and 1
   */
  calculateSimilarity(str1, str2) {
    const words1 = new Set(str1.split(" "));
    const words2 = new Set(str2.split(" "));
    
    let matches = 0;
    words1.forEach(word => {
      if (words2.has(word)) {
        matches++;
      }
    });

    const maxWords = Math.max(words1.size, words2.size);
    return matches / maxWords;
  }

  /**
   * Add new Q&A pair to knowledge base
   * @param {string} question - The question
   * @param {string} answer - The answer
   */
  addKnowledge(question, answer) {
    this.knowledgeBase[question.toLowerCase().trim()] = answer;
  }

  /**
   * Get conversation history
   * @returns {array} - Array of conversation exchanges
   */
  getHistory() {
    return this.conversationHistory;
  }

  /**
   * Clear conversation history
   */
  clearHistory() {
    this.conversationHistory = [];
  }

  /**
   * Get chatbot info
   * @returns {object} - Information about the chatbot
   */
  getInfo() {
    return {
      name: this.name,
      knowledgeBaseSize: Object.keys(this.knowledgeBase).length,
      conversationCount: this.conversationHistory.length,
    };
  }
}

module.exports = AydenAI;
