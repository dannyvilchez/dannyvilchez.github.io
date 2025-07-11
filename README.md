Hey, I’m Danny and this blog is where I dump my thoughts, code experiments, and side quests.

It’s built with [Hugo](https://gohugo.io/) and hosted on [GitHub Pages](https://pages.github.com/).

I work with finance and insurance data systems by day, so this site reflects what I do outside of my 9-5.

This blog is one of many side quests.

If it looks like I know what I’m doing, I don't, I’m just good at Googling.

## Tech Stack

- **Static Site Generator**: [Hugo](https://gohugo.io/)
- **Hosting**: GitHub Pages
- **Theme**: [Typo](https://github.com/tomfran/typo)
- **CI/CD**: GitHub Actions for auto-deploys

## Local Development

If you want to run this blog locally for some reason:

```bash
# Clone the repo
git clone https://github.com/codedannyv/codedannyv.github.io/
cd codedannyv.github.io

# Start Hugo dev server
hugo server
```

Then head to `http://localhost:1313` in your browser.

## Deployment

This blog deploys automatically via GitHub Actions every time I push to `main`.

## Structure

Here's a quick tour for the curious:

```
📁 content/       # Posts, notes, and projects
📁 static/        # Static files like images or custom JS
📁 themes/        # Theme files
hugo.toml      # Site settings, SEO bits, and lies I tell myself
```
