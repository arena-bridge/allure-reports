# Allure reports integrated functionality

Repository for storing reports

https://arena-bridge.github.io/allure-reports/#


Step by step Instruction for creating an allure-reports on Github Pages functionality, for storing automatically reports from test runs.

1. Create a Personal Access Token in GitHub → Settings → Developer settings → Personal access tokens → Tokens (classic). This will work as an authentication for pushing the reports between the two repositories.

Select:

     repo(full access)

     workflow

2. Add the token to the private repository from where the report and artifacts will be generated.

Repository → Settings → Secrets and variables → Actions → "New repository secret"

Call it: REPORTS_DEPLOY_TOKEN

3. Create a public repository where the reports will be stored

4. In GitHubs Pages in repository setting  up accordingly:

 Pages Source: 

    Deploy from a branch 

Branch:

     gh-pages / root

5. Add the given steps to the end of the .yml file:
   
   - name: Get Allure history from external repo
  uses: actions/checkout@v3
  if: always()
  continue-on-error: true
  with:
    repository: Test-Automation/reports  
    ref: gh-pages
    path: gh-pages
    token: ${{ secrets.REPORTS_DEPLOY_TOKEN }}

- name: Copy history to allure-results
  if: always()
  continue-on-error: true
  run: |
    if [ -d "gh-pages/history" ]; then
      cp -r gh-pages/history reports/allure-results/history
      echo "History copied ""
    else
      echo "No history - first run"
    fi
  
- name: Allure Report action
  uses: simple-elf/allure-report-action@master
  if: always()
  with:
    allure_results: reports/allure-results  # Location with allure generated reports
    allure_history: allure-history
    keep_reports: 20
  
- name: Deploy to public GitHub Pages repository
  if: always()
  uses: peaceiris/actions-gh-pages@v3
  with:
    personal_token: ${{ secrets.REPORTS_DEPLOY_TOKEN }}
    external_repository: Test-Automation/reports # ← Your repository
    publish_dir: allure-history  
    publish_branch: gh-pages
    keep_files: false  
    user_name: 'github-actions[bot]'
    user_email: 'github-actions[bot]@users.noreply.github.com'
    commit_message: 'Deploy test report from workflow run #${{ github.run_number }}'


6. This given script allows also for generating and storing history for previous 20 runs in the reports repository.

7. The allure reports will be accessible from the domain visible in Pages section automatically, after commit to the test-branch. 

8. There is a possibility that the URL from the Github Pages will point to the main test repository. In that case there is also a need for inserting below .yml lines, in order to fix the URL indication. 

- name: Copy and fix history
      if: always()
      continue-on-error: true
      run: |
        if [ -d "gh-pages/last-history" ]; then
          cp -r gh-pages/last-history reports/allure-results/history
          # Fix old URL's
          find reports/allure-results/history -type f -exec \
            sed -i 's|Test-Automation-example.github.io/example/|Test-Automatio-example.github.io/example-reports/|g' {} 



  - name: Fix ownership and URLs
        if: always()
        run: |
          sudo chown -R runner:runner allure-history/
          # Fix URL index.html
          sed -i 's|Test-Automation-example.github.io/example/|Test-Automatio-example.github.io/example-reports/|g' allure-history/index.html
          # Fix URL in every generated file
          find allure-history -type f -name "*.html" -o -name "*.json" | xargs \
            sed -i 's|Test-Automation-example.github.io/example/|Test-Automatio-example.github.io/example-reports/|g'
          echo "=== Fixed index.html ==="
          cat allure-history/index.html


