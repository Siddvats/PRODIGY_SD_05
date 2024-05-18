pip install requests beautifulsoup4


import requests
from bs4 import BeautifulSoup
import csv

def get_html(url):
    response = requests.get(url)
    if response.status_code == 200:
        return response.text
    return None

def parse_html(html):
    soup = BeautifulSoup(html, 'html.parser')
    products = []
    
    # Assuming the website structure has a specific class for product information
    product_elements = soup.find_all('div', class_='product-item')

    for product in product_elements:
        name = product.find('h2', class_='product-title').text.strip()
        price = product.find('span', class_='product-price').text.strip()
        rating = product.find('div', class_='product-rating').text.strip()
        products.append({'name': name, 'price': price, 'rating': rating})

    return products

def save_to_csv(products, filename):
    keys = products[0].keys()
    with open(filename, 'w', newline='', encoding='utf-8') as csvfile:
        writer = csv.DictWriter(csvfile, fieldnames=keys)
        writer.writeheader()
        writer.writerows(products)

def main():
    url = 'https://example.com/products'  # Replace with the target e-commerce URL
    html = get_html(url)
    if html:
        products = parse_html(html)
        if products:
            save_to_csv(products, 'products.csv')
            print(f'Successfully saved {len(products)} products to products.csv')
        else:
            print('No products found.')
    else:
        print('Failed to retrieve the webpage.')

if __name__ == "__main__":
    main()
