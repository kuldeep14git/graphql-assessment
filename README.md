# 🧩 Drupal 11 Headless Product Listing (GraphQL + DDEV)

This project is a Drupal 11 site configured for **headless content delivery** using **GraphQL**. It includes a sample *Product* content type, preloaded data, and a simple JavaScript `fetch()` frontend for demo purposes.

---

## 🚀 Quick Start

Make sure [DDEV](https://ddev.readthedocs.io/) is installed and configured on your machine.

### 1. Start the project

```bash
ddev start

2. Install dependencies
ddev composer install


3. Import the database
ddev import-db --src=backup.sql.gz

4. Rebuild cache
ddev drush cr

5. Login as admin
ddev drush uli
This will give you a one-time login URL to access the admin dashboard.

Optional: Generate Dummy Content
If you'd like to create new product content:

ddev drush devel-generate:content 10 --bundles=product
Requires the Devel module to be enabled.

🌐 Headless Integration: JS Fetch Example
You can use the following HTML+JavaScript to fetch and render product data:

<!DOCTYPE html>
<html>
<head>
  <title>Product List</title>
</head>
<body>
  <h1>Product List</h1>
  <div id="products"></div>

  <script>
    fetch("http://graphql-assessment.ddev.site/graphql", {
      method: "POST",
      headers: {
        "Content-Type": "application/json"
      },
      body: JSON.stringify({
        query: `
          query {
            nodeProducts(first: 10, sortKey: CREATED, reverse: true) {
              edges {
                node {
                  id
                  title
                  ... on NodeProduct {
                    price
                    description {
                      value
                    }
                    featuredImage {
                      url
                      alt
                    }
                  }
                }
              }
            }
          }
        `
      })
    })
    .then(res => res.json())
    .then(data => {
      const container = document.getElementById("products");
      data.data.nodeProducts.edges.forEach(edge => {
        const p = edge.node;
        const image = p.featuredImage ? `<img src="${p.featuredImage.url}" alt="${p.featuredImage.alt}" style="max-width:200px;"><br>` : '';
        container.innerHTML += `
          <div style="margin-bottom: 20px;">
            <strong>${p.title}</strong><br>
            ${image}
            Price: ₹${p.price}<br>
            Description: ${p.description.value}
          </div>
        `;
      });
    })
    .catch(err => console.error("GraphQL fetch error:", err));
  </script>
</body>
</html>
