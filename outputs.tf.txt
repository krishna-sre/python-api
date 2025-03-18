output "load_balancer_dns" {
  value = aws_lb.alb.dns_name
}

output "ecr_repo_url" {
  value = aws_ecr_repository.flask_repo.repository_url
}
