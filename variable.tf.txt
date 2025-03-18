variable "region" {
  default = "us-east-1a"
}

variable "app_name" {
  default = "flask-app"
}

variable "desired_count" {
  default = 2
}

variable "vpc_cidr" {
  default = "10.0.0.0/16"
}
