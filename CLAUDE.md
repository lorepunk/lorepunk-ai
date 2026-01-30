# lorepunk.ai

Personal website for Margaret Corvid (lorepunk). Dark + elegant single-page site.

## Infrastructure

- **Live URL**: https://lorepunk.ai
- - **Hosting**: Cloudflare Workers (auto-deploys on push to main)
  - - **Repo**: github.com/lorepunk/lorepunk-ai
    - - **Framework**: Astro
     
      - ## Design
     
      - - Dark theme: #0d0d0d background, warm cream accents (#c9b99a)
        - - @sarah_script calligraphy header with gradient fade into page
          - - Single scrolling page structure:
            -   1. Hero (calligraphy header image)
                2.   2. Thread (intro - who Margaret is)
                     3.   3. Work (3 columns: Content & AI, Narrative Design, Poetry)
                          4.   4. Signal (contact links)
                               5.   5. Footer (header art credit)
                                 
                                    6. ## Key Files
                                 
                                    7. - `src/pages/index.astro` - Main page content and layout
                                       - - `src/styles/global.css` - Theme colors and typography
                                         - - `src/consts.ts` - Site title and description
                                           - - `public/sarahscriptlorepunk.webp` - Header image
                                            
                                             - ## To Edit Content
                                            
                                             - Just edit `src/pages/index.astro`. The HTML is straightforward. Commits to main auto-deploy.
                                            
                                             - ## Portfolio Link
                                            
                                             - Full portfolio lives at: https://tinyurl.com/lorepunk (Notion)
                                            
                                             - ## Contact
                                            
                                             - - Email: lorepunkdoteth@gmail.com
                                               - - X/Twitter: @lorepunk
