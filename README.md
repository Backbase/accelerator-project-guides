# accelerator-project-guides
Internal repo for staging CS accelerators before moving them to their new location

## Index of accelerators provided in this repo

<details>
    <summary>BE Accelerators</summary>

Description

* [case-study-wire-mock.md](./be-accelerators/service-virtualisation-mocking-and-proxying-dependencies/case-study-wiremock/case-study-wire-mock.md)
* [customer-implementation-docker-images.md](./be-accelerators/customer-implementation-docker-images/customer-implementation-docker-images.md)
* [how-to-configure-spring-boot-admin-on-kubernetes.md](./be-accelerators/How-to-configure-Spring-Boot-Admin-on Kubernetes/how-to-configure-spring-boot-admin-on-kubernetes.md)
* [service-virtualisation-mocking-and-proxying-dependencies.md](./be-accelerators/service-virtualisation-mocking-and-proxying-dependencies/service-virtualisation-mocking-and-proxying-dependencies.md)
* [test-containers.md](./be-accelerators/test-containers/test-containers.md)

</details>

<details>
    <summary>CSE Accelerators - Digital Sales</summary>

Description



</details>

<details>
    <summary>CSE Accelerators - Identity</summary>

Description



</details>

<details>
    <summary>CSE Accelerators - Mobile</summary>

Description



</details>

<details>
    <summary>FE Accelerators</summary>

Description



</details>

<details>
    <summary>Mobile Engineering Recipes</summary>

Description



</details>

<details>
    <summary>Mobile IOS Symbiosis</summary>

Description



</details>

<details>
    <summary>Mobile SAPI</summary>

Description



</details>

<details>
    <summary>Mobile Variants</summary>

Description



</details>

<details>
    <summary>QR Journeys</summary>

Description



</details>

## Links to accelerators in external repos or locations

## How to create accelerators

(Craig / Luca can discuss how to use this repo for future development and publication of these works-in-progress)

## How this repo was created

The content in this repo was originally created in Confluence, and was converted to markdown via an automated process.  The folder structure of the individual modules represents the nesting of the topics in the original Confluence menus.

What the conversions kept:
    
* All written content and code
* Images (relatively linked - images are in same folder as markdown file)
* Linked assets such as PDF's

What the conversions did not keep:

* CSS (Some images may appear huge)
* Anchors and relative linking between markdown files (we have manually recreated these for certain index pages, but you may encounter broken links in some of the more nested files. We recommended using this page navigation)
* Favicons (sometimes)

To generate a list of all of the Markdown files, run the following from the root folder:  ```find . | grep "\.md$" > contents.txt```