# domzlab

Welcome to the source code for **domzlab.com**, the personal creative laboratory of [Dominic Manthoko](https://domzlab.com/about). 

This is where I explore and document systems architecture, cloud computing, programming experiments, and UI/UX design.

## Tech Stack

This blog is a high-performance static site built using modern web development practices:

* **Generator:** [Eleventy (11ty)](https://www.11ty.dev/) v3
* **Runtime:** Node.js v24.16.0+
* **Styling:** Custom Vanilla CSS with a premium Adaptive Reef Blue / Slate Blue theme
* **CI/CD:** Automated building and deployment using GitHub Actions
* **Hosting:** GitHub Pages
* **Domain:** `domzlab.com` (Apex domain via Namecheap DNS)

## Local Development

To run this site locally, ensure you have **Node v24.16.0** (or higher) installed. 

1. **Clone the repository:**
   ```bash
   git clone git@github.com:<your-username>/domz-lab-blog.git
   cd domz-lab-blog
   ```

2. **Install dependencies:**
   ```bash
   npm ci
   ```

3. **Start the development server:**
   ```bash
   npm start
   ```
   The site will be available at `http://localhost:8080`.

4. **Build for production:**
   ```bash
   npm run build
   ```

## Author

**Dominic Manthoko**  
*Technical Account Manager at Amazon Web Services (AWS)*  
Cloud specialist based in Cape Town, South Africa.

[Connect on LinkedIn](https://www.linkedin.com/in/dominic-manthoko/)
